package com.example.test  
import android.os.Bundle  
import android.widget.Button  
import android.widget.Toast  
import androidx.activity.ComponentActivity  
  
class MainActivity : ComponentActivity() {  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        setContentView(R.layout.activity_main)  
  
        val buttonToast = findViewById<Button>(R.id.buttonToast)  
        buttonToast.setOnClickListener {  
            Toast.makeText(this, "fvv", Toast.LENGTH_LONG).show()  
        }  
    }  
}