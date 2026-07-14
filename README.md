pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}

dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "SanCalculation"
include(":app")plugins {
    id("com.android.application") version "8.5.2" apply false
    id("org.jetbrains.kotlin.android") version "2.0.20" apply false
}plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
}

android {
    namespace = "com.san.calculation"
    compileSdk = 35

    defaultConfig {
        applicationId = "com.san.calculation"
        minSdk = 24
        targetSdk = 35
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
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }

    kotlinOptions {
        jvmTarget = "17"
    }

    buildFeatures {
        compose = true
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.15"
    }
}

dependencies {

    implementation("androidx.core:core-ktx:1.13.1")
    implementation("androidx.activity:activity-compose:1.9.1")

    implementation("androidx.compose.ui:ui:1.6.8")
    implementation("androidx.compose.material3:material3:1.2.1")
    implementation("androidx.compose.ui:ui-tooling-preview:1.6.8")

    debugImplementation("androidx.compose.ui:ui-tooling:1.6.8")
}<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:label="San Calculation"
        android:icon="@mipmap/ic_launcher"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.SanCalculation">

        <activity
            android:name=".MainActivity"
            android:exported="true">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

    </application>

</manifest>package com.san.calculation

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.ui.Modifier

class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            MaterialTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    CalculatorScreen()
                }
            }
        }
    }
}package com.san.calculation

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun CalculatorScreen() {

    var display by remember { mutableStateOf("0") }

    val buttons = listOf(
        listOf("C", "⌫", "%", "÷"),
        listOf("7", "8", "9", "×"),
        listOf("4", "5", "6", "-"),
        listOf("1", "2", "3", "+"),
        listOf("0", ".", "=")
    )

    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color(0xFF0F172A))
            .padding(16.dp)
    ) {

        Spacer(modifier = Modifier.height(40.dp))

        Text(
            text = display,
            fontSize = 48.sp,
            color = Color.White,
            textAlign = TextAlign.End,
            modifier = Modifier
                .fillMaxWidth()
                .padding(20.dp)
        )

        Spacer(modifier = Modifier.height(20.dp))

        buttons.forEach { row ->

            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.spacedBy(12.dp)
            ) {

                row.forEach { text ->

                    Button(
                        onClick = {
                            display = CalculatorEngine.calculate(display, text)
                        },
                        modifier = Modifier
                            .weight(1f)
                            .height(70.dp),
                        shape = RoundedCornerShape(20.dp)
                    ) {

                        Text(
                            text = text,
                            fontSize = 26.sp,
                            fontWeight = FontWeight.Bold
                        )
                    }
                }
            }

            Spacer(modifier = Modifier.height(12.dp))
        }
    }
}package com.san.calculation

object CalculatorEngine {

    fun calculate(current: String, input: String): String {

        return when (input) {

            "C" -> "0"

            "⌫" -> {
                if (current.length > 1)
                    current.dropLast(1)
                else
                    "0"
            }

            "=" -> {
                try {
                    val expression = current
                        .replace("×", "*")
                        .replace("÷", "/")

                    val result = SimpleExpression.evaluate(expression)

                    if (result % 1 == 0.0)
                        result.toLong().toString()
                    else
                        result.toString()

                } catch (e: Exception) {
                    "Error"
                }
            }

            else -> {
                if (current == "0")
                    input
                else
                    current + input
            }
        }
    }
}

object SimpleExpression {

    fun evaluate(expression: String): Double {

        val tokens = expression
            .replace("+", " + ")
            .replace("-", " - ")
            .replace("*", " * ")
            .replace("/", " / ")
            .trim()
            .split("\\s+".toRegex())

        if (tokens.isEmpty()) return 0.0

        var result = tokens[0].toDouble()

        var i = 1

        while (i < tokens.size - 1) {

            val op = tokens[i]
            val value = tokens[i + 1].toDouble()

            when (op) {
                "+" -> result += value
                "-" -> result -= value
                "*" -> result *= value
                "/" -> result /= value
            }

            i += 2
        }

        return result
    }
}package com.san.calculation.ui.theme

import androidx.compose.ui.graphics.Color

// Premium Dark Theme
val Background = Color(0xFF0F172A)
val Surface = Color(0xFF111827)

val NumberButton = Color(0xFF1F2937)
val OperatorButton = Color(0xFF3B82F6)
val EqualButton = Color(0xFF10B981)

val DisplayText = Color(0xFFFFFFFF)
val SecondaryText = Color(0xFF94A3B8)

// Light Theme (optional)
val LightBackground = Color(0xFFF8FAFC)
val LightSurface = Color(0xFFFFFFFF)
val LightText = Color(0xFF111827)package com.san.calculation.ui.theme

import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.runtime.Composable

private val DarkColors = darkColorScheme(
    primary = OperatorButton,
    secondary = EqualButton,
    background = Background,
    surface = Surface,
    onPrimary = DisplayText,
    onSecondary = DisplayText,
    onBackground = DisplayText,
    onSurface = DisplayText
)

@Composable
fun SanCalculationTheme(
    content: @Composable () -> Unit
) {
    MaterialTheme(
        colorScheme = DarkColors,
        typography = Typography,
        content = content
    )
}name: Build APK

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Grant execute permission
        run: chmod +x gradlew

      - name: Build Debug APK
        run: ./gradlew assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: SanCalculation-APK
          path: app/build/outputs/apk/debug/app-debug.apk