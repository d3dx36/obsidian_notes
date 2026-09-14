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
- [ ] Обложки — `Image` + **Coil** / Glide
- [ ] Material 3 — стиль и тема

### 4. Уведомления и фон
- [ ] Foreground Service — воспроизведение в фоне
- [ ] `MediaNotification` (Media3) или кастомные уведомления
- [ ] Media3 `MediaLibraryService` — библиотека медиа (опционально)

### 5. Архитектура
- [ ] MVVM / MVI
- [ ] DI — Hilt или Koin
- [ ] Repository Pattern

---

## Стек технологий

| Компонент | Библиотека |
|-----------|-----------|
| Плеер | `androidx.media3` (Media3) |
| UI | Jetpack Compose + Material 3 |
| DI | Hilt / Koin |
| Загрузка обложек | Coil |
| Навигация | Compose Navigation |
| Аудио-фокус | AudioManager API |

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
// ViewModel
@HiltViewModel
class PlayerViewModel @Inject constructor(
    private val context: Application
) : ViewModel() {

    private var mediaController: MediaController? = null
    val isPlaying = mutableStateOf(false)
    val position = mutableLongStateOf(0L)
    val duration = mutableLongStateOf(0L)

    fun initialize(uri: Uri) {
        val sessionToken = MediaSession.Builder(context, player).build().sessionToken
        mediaController = MediaController.Builder(context, sessionToken).buildAsync().get()
        mediaController?.prepare()
        mediaController?.play()
    }

    fun playPause() {
        if (isPlaying.value) mediaController?.pause()
        else mediaController?.play()
    }

    fun seekTo(position: Long) {
        mediaController?.seekTo(position)
    }
}

// Compose UI
@Composable
fun PlayerScreen(viewModel: PlayerViewModel = hiltViewModel()) {
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        // Обложка
        AsyncImage(model = currentTrack.coverUrl, contentDescription = null)

        // Название трека
        Text(text = currentTrack.title, style = MaterialTheme.typography.headlineSmall)

        // Seek-бар
        Slider(
            value = position.toFloat(),
            onValueChange = { viewModel.seekTo(it.toLong()) },
            valueRange = 0f..duration.toFloat()
        )

        // Кнопки управления
        Row(horizontalArrangement = Arrangement.Center) {
            IconButton(onClick = { viewModel.skipToPrevious() }) {
                Icon(Icons.Default.SkipPrevious, contentDescription = "Previous")
            }
            IconButton(onClick = { viewModel.playPause() }) {
                Icon(
                    if (isPlaying) Icons.Default.Pause else Icons.Default.PlayArrow,
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

---

## Ресурсы

- [Media3 — Developer Guide](https://developer.android.com/media/media3)
- [Jetpack Compose — Documentation](https://developer.android.com/jetpack/compose)
- [Universal Android Music Player (Google)](https://github.com/android/uamp)
- [Compose Accompanist — Media](https://google.github.io/accompanist/media3/)

---

## Чеклист готовности

- [ ] Проект создаётся и запускается
- [ ] Воспроизведение одного трека работает
- [ ] Seek-бар отображает позицию и позволяет перемотку
- [ ] Кнопки play/pause/prev/next работают
- [ ] Уведомление с управлением появляется
- [ ] Фоновое воспроизведение работает (Service)
- [ ] Аудио-фокус обрабатывается (пауза при звонке)
