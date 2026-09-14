Этот класс делает доступными **автоматические анимации изменений** в контейнере при добавлении/удалении элементов.

Самый простой способ активировать такие анимации из родительского контейнера — задать атрибут `android:animateLayoutChanges="true"` и установить логику добавления /удаления дочернего _View_ в контейнер.

Вот как это выглядит:

![рисунок](https://lms-cdn.skillfactory.ru/assets/courseware/v1/9d813daa7e15c52d758c70e70ec57dd1/asset-v1:SkillFactory+ANDROID-NEW+2020+type@asset+block/ANDROID_25.7_1.gif)

Вот код добавления/удаления:

button_add.setOnClickListener {
   val button = Button(this)
   button.text = "Button"

   container.addView(button)
}

button_remove.setOnClickListener {
   if(container.childCount != 0) {
       container.removeViewAt(container.childCount - 1)
   }
}

**Важно:** при удалении _View_ из контейнера нужно проверить, есть ли там _View._ Если этого не сделать, то приложение упадет с _NPE (NullPointerException)_.

Если мы только добавляем и удаляем элементы, то всё прекрасно, но если нам нужно изменять элементы, то стандартного функционала становится недостаточно. Вот, к примеру, при нажатии на кнопку меняется её размер из-за длины текста:

![рисунок](https://lms-cdn.skillfactory.ru/assets/courseware/v1/ee7c0f96f4f163cc17a600feb34f0442/asset-v1:SkillFactory+ANDROID-NEW+2020+type@asset+block/ANDROID_25.7_2.gif)

Анимации нет, а по умолчанию нам доступны анимации только добавления/удаления. Чтобы это исправить, надо нашему контейнеру добавить новые свойства, делается это так:

container.layoutTransition.enableTransitionType(LayoutTransition.CHANGING)

То есть у нашего контейнера с атрибутом `android:animateLayoutChanges="true"` получаем `**layoutTransition**` и устанавливаем ему дополнительное свойство. Теперь анимация изменения дочернего _View_ будет такая:

![рисунок](https://lms-cdn.skillfactory.ru/assets/courseware/v1/c652ae2c78ac09b10ef3eead5e438469/asset-v1:SkillFactory+ANDROID-NEW+2020+type@asset+block/ANDROID_25.7_3.gif)

Вы можете добавлять **свою анимацию**. Давайте сделаем новую анимацию добавления, пусть она будет такая:

![рисунок](https://lms-cdn.skillfactory.ru/assets/courseware/v1/e5f89f0dd710f64855637b3bf0226963/asset-v1:SkillFactory+ANDROID-NEW+2020+type@asset+block/ANDROID_25.7_4.gif)

Для этого нам надо установить новую анимацию для нашего `layoutTransition` вот так:

container.layoutTransition.setAnimator(LayoutTransition.APPEARING, AnimatorInflater.loadAnimator(this, R.animator.animator))

Если с первой частью кода всё понятно, то разберём вторую часть, а именно вот этот код:

AnimatorInflater.loadAnimator(this, R.animator.animator)

Нам для анимации нужен объект `Animator`, его мы можем получить, например, через `ObjectAnimator`. Но, если мы его будем создавать в коде, то нам нужен будет объект, на котором он будет работать. Мы такой объект предоставить не можем, поэтому нам нужно создать `ObjectAnimator` через XML. Как это делается?

Для начала нам нужна специальная папка ресурсов для этих объектов, называется она `**animator**`. Добавляем её, кликнув правой кнопкой мыши по папке `res`, и нажимаем `**New**-> **Android Resource Directory**`:

![рисунок](https://lms-cdn.skillfactory.ru/assets/courseware/v1/654e41f4d3c9740f6c8c70c7e866ffdf/asset-v1:SkillFactory+ANDROID-NEW+2020+type@asset+block/ANDROID_25.7_5.png)

В открывшемся окне выбираем тип ресурса `**animator**`, жмём `**OK**`:

![рисунок](https://lms-cdn.skillfactory.ru/assets/courseware/v1/d9d2084324e8adbff11347aa77bf55e6/asset-v1:SkillFactory+ANDROID-NEW+2020+type@asset+block/ANDROID_25.7_6.png)

Далее в этой папке создаем наш аниматор, делается это так:

<?xml version="1.0" encoding="utf-8"?>
<set>
   <objectAnimator
       android:duration="500"
       android:propertyName="scaleX"
       android:valueFrom="0"
       android:valueTo="1.0"
       android:interpolator="@android:interpolator/bounce"
       android:valueType="floatType"
       xmlns:android="http://schemas.android.com/apk/res/android" />

   <objectAnimator
       android:duration="500"
       android:propertyName="scaleY"
       android:valueFrom="0"
       android:valueTo="1.0"
       android:interpolator="@android:interpolator/bounce"
       android:valueType="floatType"
       xmlns:android="http://schemas.android.com/apk/res/android" />
</set>

Синтаксис вам уже знаком, новый для вас только тег `objectAnimator`.

Всё готово, осталось только при создании кнопок устанавливать им нулевой масштаб, чтобы анимация была правильной:

button.scaleX = 0f
button.scaleY = 0f