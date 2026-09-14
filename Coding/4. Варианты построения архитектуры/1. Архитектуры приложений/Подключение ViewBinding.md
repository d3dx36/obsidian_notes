`build.gradle` модуля `app`:

android {
   ...
   buildFeatures {
       `viewBinding = true`
   }
   ...
}


`MainActivity.kt`

```
class MainActivity : AppCompatActivity() {

   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)       //Ссылка на binging позволяет получить все XML-объекты, у которых есть id       
       val binding = ActivityMainBinding.inflate(layoutInflater)
       setContentView(binding.root)
       binding.button.setOnClickListener {
           Toast.makeText(this, "ViewBinding in Activity", Toast.LENGTH_SHORT).show()
       }
   }
}
```