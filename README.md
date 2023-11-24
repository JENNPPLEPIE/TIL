// 1주차 온보딩 

/////////////////////////////////////////////////////////////////////////////////// 첫번째 [My BMI calculator] - FIN

// MainActivity
package com.example.mybmi_calculator

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.Toast
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val heightEditText = findViewById<EditText>(R.id.et_height)
        val weightEditText = findViewById<EditText>(R.id.et_weight)
        val submitButton = findViewById<Button>(R.id.btn_check)

        submitButton. setOnClickListener {

            if (heightEditText.text.isEmpty()) {
                Toast.makeText(this, "신장을 입력해주새요.", Toast.LENGTH_SHORT).show()
                return@setOnClickListener
            }
            if (weightEditText.text.isEmpty()) {
                Toast.makeText(this, "체중을 입력해주새요.", Toast.LENGTH_SHORT).show()
                return@setOnClickListener
            }
            val height : Int = heightEditText.text.toString().toInt()
            val weight : Int = weightEditText.text.toString().toInt()

            val intent = Intent(this, ResultActivity::class.java)
            intent.putExtra("height", height)
            intent.putExtra("weight", weight)
            startActivity(intent)
        }
    }
}


//ResultActivity
package com.example.mybmi_calculator

import android.graphics.Color
import android.os.Bundle
import android.widget.Button
import android.widget.ImageView
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import kotlin.math.pow
import kotlin.math.round

class ResultActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_result)

        val height = intent.getIntExtra("height", 0)
        val weight = intent.getIntExtra("weight", 0)

        //BMI 계산
        var value = weight / (height/100.0).pow(2.0)
        value = round(value*10)/10

        var resultText = ""
        var resImage = 0
        var resColor = 0

        if(value < 18.5) {
            resultText = "저체중"
            resImage = R.drawable.lv_1
            resColor = Color.YELLOW
        } else if(value >= 18.5 && value < 23.0) {
            resultText = "정상 체중"
            resImage = R.drawable.lv_2
            resColor = Color. GREEN
        } else if(value >= 23.0 && value < 25.0) {
            resultText = "과체중"
            resImage = R.drawable.lv_3
            resColor = Color. BLACK
        } else if(value >= 25.0 && value < 30.0) {
            resultText = "경도비만"
            resImage = R.drawable.lv_4
            resColor = Color. CYAN
        } else if(value >= 30.0 && value < 35.0) {
            resultText = "중정도 비만"
            resImage = R.drawable.lv_5
            resColor = Color. MAGENTA
        } else {
            resultText = "고도 비만"
            resImage = R.drawable.lv_6
            resColor = Color. RED
        }
        val tv_resValue = findViewById<TextView>(R.id.tv_resvalue)
        val tv_resText = findViewById<TextView>(R.id.tv_res_text)
        val iv_Image= findViewById<ImageView>(R.id.iv_image)
        val btn_back = findViewById<Button>(R.id.btn_back)

        tv_resValue.text = value.toString()
        tv_resText.text = resultText
        iv_Image.setImageResource(resImage)
        tv_resText.setTextColor(resColor)

        btn_back.setOnClickListener {
            finish()
        }
    }
}




///////////////////////////////////////////////////////////////////////////////////////////////////////////////// 두번째 [My Lottery] -FIN

// MainActivity
package com.example.myloterry

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import android.widget.NumberPicker
import android.widget.TextView
import android.widget.Toast
import androidx.core.content.ContextCompat
import androidx.core.view.isVisible
import org.w3c.dom.Text

class MainActivity : AppCompatActivity() {

    private val clearButton by lazy { findViewById<Button>(R.id.btn_clear) }
    private val addButton by lazy { findViewById<Button>(R.id.btn_add) }
    private val runButton by lazy { findViewById<Button>(R.id.btn_run) }
    private val numPick by lazy { findViewById<NumberPicker>(R.id.np_num) }

    private val numTextViewList : List<TextView> by lazy {
        listOf<TextView> (
            findViewById(R.id.tv_num1),
            findViewById(R.id.tv_num2),
            findViewById(R.id.tv_num3),
            findViewById(R.id.tv_num4),
            findViewById(R.id.tv_num5),
            findViewById(R.id.tv_num6)
        )
    }

    private var didRun = false
    private val pickNumberSet = hashSetOf<Int>()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        numPick.minValue = 1
        numPick.maxValue = 45

        initAddButton()
        initRunButton()
        initClearButton()
    }

    private fun initAddButton(){
        addButton.setOnClickListener {
            when {
                didRun -> showToast("초기화 후에 시도하세요.")
                pickNumberSet.size >= 5 -> showToast("숫자는 최대 5개까지 선택할 수 있습니다.")
                pickNumberSet.contains(numPick.value) -> showToast("이미 선택된 숫자입니다.")
                else -> {
                    val textView = numTextViewList[pickNumberSet.size]
                    textView.isVisible = true
                    textView.text = numPick.value.toString()

                    setNumBack(numPick.value, textView)
                    pickNumberSet.add(numPick.value)
                }
            }
        }
    }

    private fun initRunButton() {
        runButton.setOnClickListener {
            val list = getRandom()

            didRun = true

            list.forEachIndexed { index, number ->
                val textView = numTextViewList[index]
                textView.text = number.toString()
                textView.isVisible = true
                setNumBack(number, textView)
            }
        }
    }

    private fun initClearButton() {

        clearButton.setOnClickListener {
            pickNumberSet.clear()
            numTextViewList.forEach{it.isVisible=false}
            didRun = false
            numPick.value = 1
        }
    }


    private fun getRandom(): List<Int> {
        val numbers = (1..45).filter {it !in pickNumberSet}
        return (pickNumberSet + numbers.shuffled().take(6-pickNumberSet.size)).sorted()
    }

    private fun setNumBack(number:Int, textView: TextView) {
        val background = when(number) {
            in 1..10 -> R.drawable.circle_yellow
            in 11..20 -> R.drawable.circle_blue
            in 21..31 -> R.drawable.circle_red
            in 31..40 -> R.drawable.circle_gray
            else -> R.drawable.circle_green
        }
        textView.background = ContextCompat.getDrawable(this, background)
    }


    private fun showToast(message:String) {
        Toast.makeText( this, message, Toast.LENGTH_SHORT).show()
    }
}



//Activity MAin

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="300dp"

        android:layout_height="100dp"
        android:layout_marginTop="30dp"
        android:src="@drawable/lotto"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="로또 번호 생성기"
        android:textColor="#3F51B5"
        android:textSize="30sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/imageView" />

    <NumberPicker
        android:id="@+id/np_num"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="40dp"
        android:background="#FFEB3B"
        android:solidColor="#9F8431"

        app:layout_constraintEnd_toStartOf="@+id/iv_left"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/tv_title" />

    <ImageView
        android:id="@+id/iv_left"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:layout_marginStart="16dp"
        android:src="@drawable/ic_left"
        app:layout_constraintBottom_toBottomOf="@+id/np_num"
        app:layout_constraintEnd_toStartOf="@+id/btn_add"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toEndOf="@+id/np_num"
        app:layout_constraintTop_toTopOf="@+id/np_num" />

    <Button
        android:id="@+id/btn_add"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:text="번호 추가하기"
        app:layout_constraintBottom_toBottomOf="@+id/iv_left"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toEndOf="@+id/iv_left"
        app:layout_constraintTop_toTopOf="@+id/iv_left"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:layout_marginStart="16dp"
        android:layout_marginEnd="16dp"
        android:layout_marginTop="32dp"
        android:orientation="horizontal"
        android:background="@drawable/bg"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/np_num">

        <TextView
            android:id="@+id/tv_num1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:background="@drawable/circle_gray"
            android:gravity="center"
            android:text="1"
            android:textColor="@color/white"
            android:textSize="18sp"
            android:textStyle="bold"
            android:visibility="gone"/>

        <TextView
            android:id="@+id/tv_num2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:background="@drawable/circle_blue"
            android:gravity="center"
            android:text="1"
            android:textColor="@color/white"
            android:textSize="18sp"
            android:textStyle="bold"
            android:visibility="gone"/>

        <TextView
            android:id="@+id/tv_num3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:background="@drawable/circle_gray"
            android:gravity="center"
            android:text="1"
            android:textColor="@color/white"
            android:textSize="18sp"
            android:textStyle="bold"
            android:visibility="gone"/>

        <TextView
            android:id="@+id/tv_num4"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:background="@drawable/circle_green"
            android:gravity="center"
            android:text="1"
            android:textColor="@color/white"
            android:textSize="18sp"
            android:textStyle="bold"
            android:visibility="gone"/>

        <TextView
            android:id="@+id/tv_num5"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:background="@drawable/circle_red"
            android:gravity="center"
            android:text="1"
            android:textColor="@color/white"
            android:textSize="18sp"
            android:textStyle="bold"
            android:visibility="gone"/>

        <TextView
            android:id="@+id/tv_num6"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="5dp"
            android:background="@drawable/circle_yellow"
            android:gravity="center"
            android:text="1"
            android:textColor="@color/white"
            android:textSize="18sp"
            android:textStyle="bold"
            android:visibility="gone">

        </TextView>

    </LinearLayout>

    <Button
        android:id="@+id/btn_run"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="자동 생성 시작"
        android:layout_marginStart="16dp"
        android:layout_marginBottom="16dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@+id/btn_clear"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent" />

    <Button
        android:id="@+id/btn_clear"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="초기화"
        android:layout_margin="16dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toEndOf="@+id/btn_run" />


</androidx.constraintlayout.widget.ConstraintLayout>



//build.gradle.kts

lugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
}

android {
    namespace = "com.example.myloterry"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.example.myloterry"
        minSdk = 26
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = "1.8"
    }
}

dependencies {

    implementation("androidx.core:core-ktx:1.9.0")
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.10.0")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
}


//bg.xml

<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid
        android:color="#D6EDEB"/>
    <stroke
        android:width="1dp"
        android:color="#000000"/>
</shape>



///////////////////////////////////////////////////////////////////////////////////////////////////////////////// 세번째 - ing (주말에 마무리할 예정)
