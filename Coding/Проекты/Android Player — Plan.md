# Android Медиаплеер — Kotlin + Jetpack Compose

## Обзор

Создание медиаплеера на Android с использованием Kotlin и Jetpack Compose.
Современный стек: **Media3 (ExoPlayer)** + **Compose** + **MVVM**.

---

## Что изучить

### 1. Основы
- [ ] Kotlin — корутины, Flow, sealed-классы
- [ ] Jetpack Compose — композиции, state, навигация
- [ ] Android Lifecycle — Activity/ViewModel lifecycle

### 2. Медиа-воспроизведение
- [ ] **Media3 (ExoPlayer)** — `MediaController`, `MediaSession`, `MediaSessionService`
- [ ] **MediaSession** — управление из уведомлений, гарнитуры, Android Auto
- [ ] **AudioManager** — аудио-фокус (`AudioFocusRequest`)

### 3. UI плеера
- [ ] Compose — `Slider` (seek-бар), анимации
- [ ] Обложки — `Image` + **Coil 3**
- [ ] Material 3 — стиль и тема

### 4. Уведомления и фон
- [ ] Foreground Service — воспроизведение в фоне
- [ ] **Обязательно:** `android:foregroundServiceType="mediaPlayback"` в манифесте (Android 14+ требует тип)
- [ ] **Обязательно:** запрос `POST_NOTIFICATIONS` в runtime (Android 13+), иначе уведомление не покажется
- [ ] Media3 `MediaLibraryService` — библиотека медиа (опционально)

### 5. Архитектура
- [ ] MVVM / MVI
- [ ] DI — Hilt или Koin
- [ ] Repository Pattern

---

## Стек технологий

| Компонент | Библиотека |
|-----------|-----------|
| Плеер | `androidx.media3:media3-exoplayer:1.11.1` |
| UI | Jetpack Compose + Material 3 (`compose.material3:material3:1.4.0`) |
| DI | Hilt 2.60.1 / Koin 4.2.2 |
| Загрузка обложек | `io.coil-kt.coil3:coil-compose:3.6.3` |
| Навигация | Compose Navigation |
| Аудио-фокус | AudioManager API |

> [!warning] Coil: новая группа, новое имя
> Coil 2 (`io.coil-kt:coil-compose`) застрял на версии 2.7.0 и больше не развивается. Актуальный Coil 3 живёт в **другой группе** — `io.coil-kt.coil3`. Просто поменять цифру в названии нельзя, меняется и `groupId`, и API: `coil3.request.ImageRequest` вместо `coil.request.ImageRequest`.

---

## Структура проекта

```
app/
├── ui/
│   ├── player/
│   │   ├── PlayerScreen.kt        # Compose UI
│   │   ├── PlayerViewModel.kt     # State + контроллер
│   │   └── PlayerComponents.kt    # Кнопки, seek-бар, обложка
│   └── playlist/
│       └── PlaylistScreen.kt
├── service/
│   └── MusicService.kt            # MediaSessionService
├── data/
│   ├── model/
│   │   └── Track.kt               # Данные трека
│   └── repository/
│       └── MusicRepository.kt     # Источник треков
└── di/
    └── AppModule.kt               # Hilt-модули
```

---

## Минимальный плеер (псевдокод)

```kotlin
// Состояние экрана — единственный источник правды
data class PlayerState(
    val title: String = "",
    val isPlaying: Boolean = false,
    val position: Long = 0L,
    val duration: Long = 0L,
)

// ViewModel
@HiltViewModel
class PlayerViewModel @Inject constructor(
    private val context: Application
) : ViewModel() {

    private var mediaController: MediaController? = null

    // StateFlow, а НЕ mutableStateOf: ViewModel не должен зависеть от Compose
    private val _state = MutableStateFlow(PlayerState())
    val state: StateFlow<PlayerState> = _state.asStateFlow()

    private val playerListener = object : Player.Listener {
        override fun onEvents(player: Player, events: Player.Events) {
            // Media3 сам умеет отдавать актуальное состояние
            _state.update {
                it.copy(
                    isPlaying = player.isPlaying,
                    position = player.currentPosition,
                    duration = player.duration,
                )
            }
        }
    }

    fun initialize(uri: Uri) {
        // Player и MediaSession живут в сервисе, а не в ViewModel
        val sessionToken = MediaSession.Builder(context, player).build().sessionToken
        mediaController = MediaController.Builder(context, sessionToken).buildAsync().get()
        mediaController?.addListener(playerListener)
        mediaController?.setMediaItem(uri.toMediaItem())
        mediaController?.prepare()
        mediaController?.play()
    }

    fun playPause() {
        if (state.value.isPlaying) mediaController?.pause()
        else mediaController?.play()
    }

    fun seekTo(position: Long) = mediaController?.seekTo(position)

    override fun onCleared() {
        // Освобождаем ресурсы, ViewModel знает свой жизненный цикл
        mediaController?.removeListener(playerListener)
        mediaController?.release()
        mediaController = null
    }
}

// Compose UI
@Composable
fun PlayerScreen(viewModel: PlayerViewModel = hiltViewModel()) {

    val state by viewModel.state.collectAsStateWithLifecycle()

    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        // Обложка
        AsyncImage(model = state.title, contentDescription = null)

        Text(text = state.title, style = MaterialTheme.typography.headlineSmall)

        Slider(
            value = state.position.toFloat(),
            onValueChange = { viewModel.seekTo(it.toLong()) },
            valueRange = 0f..state.duration.toFloat()
        )

        Row(horizontalArrangement = Arrangement.Center) {
            IconButton(onClick = { viewModel.skipToPrevious() }) {
                Icon(Icons.Default.SkipPrevious, contentDescription = "Previous")
            }
            IconButton(onClick = { viewModel.playPause() }) {
                Icon(
                    if (state.isPlaying) Icons.Default.Pause else Icons.Default.PlayArrow,
                    contentDescription = "Play/Pause"
                )
            }
            IconButton(onClick = { viewModel.skipToNext() }) {
                Icon(Icons.Default.SkipNext, contentDescription = "Next")
            }
        }
    }
}
```

> [!danger] Почему не `mutableStateOf`
> `mutableStateOf` и `mutableLongStateOf` — это **Compose-рантайм**. Если ViewModel отдаёт наружу Compose-стейт, он:
> 1. тянет за собой `androidx.compose.runtime` и перестаёт быть тестируемым без Compose;
> 2. ломает однонаправленный поток данных — источником правды становится UI, а не модель;
> 3. не переживает отмену подписки так, как это умеет `StateFlow`.
>
> Наружу торчит `StateFlow`, Compose подписывается через `collectAsStateWithLifecycle()`, который **автоматически отписывается**, когда Activity уходит в фон. Обратите внимание: в коде выше нет импорта `getValue` для `by` — он нужен, но добавляется IDE автоматически.

## Разрешения и манифест

Без этих вещей сервис не запустится на современных версиях Android.

```xml
<manifest ...>

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PLAYBACK" />
    <!-- Android 13+ : без этого уведомление не покажется вообще -->
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

    <application ...>
        <service
            android:name=".service.MusicService"
            android:exported="true"
            android:foregroundServiceType="mediaPlayback">
            <intent-filter>
                <action android:name="androidx.media3.session.MediaSessionService" />
                <action android:name="android.media.browse.MediaBrowserService" />
            </intent-filter>
        </service>
    </application>
</manifest>
```

Запрос `POST_NOTIFICATIONS` в рантайме:

```kotlin
val launcher = rememberLauncherForActivityResult(
    ActivityResultContracts.RequestPermission()
) { granted -> /* без этого уведомление не появится */ }

LaunchedEffect(Unit) {
    if (ContextCompat.checkSelfPermission(context, Manifest.permission.POST_NOTIFICATIONS)
        != PackageManager.PERMISSION_GRANTED
    ) {
        launcher.launch(Manifest.permission.POST_NOTIFICATIONS)
    }
}
```

---

## Ресурсы

- [Media3 — Developer Guide](https://developer.android.com/media/media3)
- [Compose — документация](https://developer.android.com/jetpack/compose)
- [Universal Android Music Player (Google)](https://github.com/android/uamp)
- [Coil 3](https://coil-kt.github.io/coil/)
- [Media3 UI Components](https://developer.android.com/media/media3/exoplayer/media3-session)

> [!note] Accompanist больше не нужен
> В старом плане была ссылка на `google.github.io/accompanist/media3/`. Библиотека **заморожена с апреля 2025** (последний релиз 0.37.3) и объявлена устаревшей. Всё, что она давала, переехало в Media3 и AndroidX. Не трать на неё время.

---

## Чеклист готовности

- [ ] Проект создаётся и запускается
- [ ] Воспроизведение одного трека работает
- [ ] Seek-бар отображает позицию и позволяет перемотку
- [ ] Кнопки play/pause/prev/next работают
- [ ] Уведомление появляется (проверить на Android 13+, где нужен `POST_NOTIFICATIONS`)
- [ ] Фоновое воспроизведение работает (Service с `foregroundServiceType="mediaPlayback"`)
- [ ] Аудио-фокус обрабатывается (пауза при звонке)
- [ ] Edge-to-edge: контент не уезжает под статус-бар и навигацию
- [ ] ViewModel отдаёт наружу `StateFlow`, а не Compose-стейт
