# 📘 **R8 & ProGuard — The Ultimate Beginner-Friendly Notes (100% Clear & Practical)**

# 🧠 **1. What Exactly Is R8? (Simple Explanation)**

R8 is the **default code shrinker for Android**.
It runs automatically when you enable:

```gradle
minifyEnabled true
```

### R8 does 3 important things:

1️⃣ **Shrinking** → Removes unused classes, functions, resources
2️⃣ **Obfuscation** → Renames classes & methods → makes hacking harder
3️⃣ **Optimization** → Inlines code, removes dead code, improves performance

> It replaces **ProGuard** completely since 2019 (Android Gradle Plugin 3.4+).

---

# 🧠 **2. What is ProGuard Then? (Simple)**

ProGuard is the **old shrinker** used before R8.
Now it is **only used for its rules file**:

👉 `proguard-rules.pro`

Even though R8 replaced ProGuard,
**R8 understands and uses ProGuard rules syntax**, so rules remain same.

---

# 🔄 **3. R8 vs ProGuard – Crystal Clear Difference**

| Feature             | ProGuard | R8             |
| ------------------- | -------- | -------------- |
| Shrinker            | Yes      | Yes            |
| Optimizer           | Yes      | Yes (Better)   |
| Obfuscator          | Yes      | Yes            |
| Speed               | ❌ Slow   | ✔ Fast         |
| APK Size            | ❌ Larger | ✔ Much smaller |
| Default in Android? | ❌ No     | ✔ Yes          |

**R8 = Faster, better, modern ProGuard replacement.**

---

# 🏗 **4. How R8 Works in the Build Pipeline**

```
Your Kotlin/Java code
       ↓
  D8 (converts to DEX)
       ↓
  R8 (shrinks + obfuscates + optimizes)
       ↓
Final APK / AAB
```

---

# 🛠 **5. How To Enable R8 in Android Project**

R8 is already enabled by default.
You only need to turn ON shrinking for release builds:

```gradle
buildTypes {
    release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    }
}
```

### What these mean:

* `minifyEnabled true` → Turns on R8
* `shrinkResources true` → Removes unused drawable/xml
* `proguardFiles` → Where you keep your keep rules

---

# 📄 **6. What is proguard-rules.pro?**

A file where you tell R8:

✔ What **NOT to remove**
✔ What **NOT to rename**
✔ What **NOT to optimize**

So your app won't crash.

Example location:

```
app/proguard-rules.pro
```

---

# 🔐 **7. Why Do Apps Crash Without Keep Rules?**

Because R8 removes or renames classes that **reflection-based libraries** need.

Example:

* Gson / Moshi
* Retrofit
* Room
* Hilt / Dagger
* WorkManager
* Firebase
* Glide / Coil

They all use reflection → R8 can’t “see” them → must protect them.

---

# 🧷 **8. Most Important — Keep Rules (The Heart of R8)**

### ✳ 1. Keep a full class

```pro
-keep class com.example.model.User { *; }
```

### ✳ 2. Keep only public methods

```pro
-keepclassmembers class com.example.api.* {
    public *;
}
```

### ✳ 3. Keep class names only (no fields)

```pro
-keepnames class com.example.MyClass
```

### ✳ 4. Prevent obfuscation

```pro
-keepclassmembers class * {
    @com.google.gson.annotations.SerializedName <fields>;
}
```

---

# 📦 **9. Ready-To-Use Rules for Popular Libraries**

### 🔹 Gson Models

```pro
-keep class com.example.model.** { *; }
-keepclassmembers class com.example.model.** {
    <fields>;
}
```

### 🔹 Retrofit + OkHttp

```pro
-dontwarn okhttp3.**
-dontwarn okio.**
-dontwarn retrofit2.**

-keep class retrofit2.* { *; }
-keep class okhttp3.* { *; }
```

### 🔹 Room

```pro
-keepclassmembers class * {
    @androidx.room.* <methods>;
}
```

### 🔹 Hilt / Dagger

```pro
-keep class dagger.hilt.** { *; }
-dontwarn dagger.hilt.internal.**
```

### 🔹 ViewModels (important)

```pro
-keep class * extends androidx.lifecycle.ViewModel { *; }
```

---

# 🧪 **10. How to Test if R8 is Working?**

Run a release build:

```
Build → Generate Signed APK/AAB
```

Check `app/build/outputs/mapping/release/mapping.txt`

Inside mapping.txt:

✔ Original name → Obfuscated name
✔ You can use it to read crash logs

---

# 📉 **11. Common R8 Problems & Fixes**

### ❌ App crashes after enabling R8

Your keep rules are wrong.

✔ Add rules for your models
✔ Add rules for libraries

---

### ❌ Retrofit response gives null

Solution: Keep model fields

```pro
-keepclassmembers class com.example.model.** { <fields>; }
```

---

### ❌ MissingClassException

Solution: Add keep rule to prevent deletion

```pro
-keep class missing.class.Name
```

---

# 🧠 **12. Pro Tips to Become R8 Pro**

- ✔ Enable it from day 1 in your project (not at the end)
- ✔ Keep rules for each new library you add
- ✔ Always check `mapping.txt` on crashes
- ✔ Use `shrinkResources true` to reduce APK size
- ✔ Avoid over-using `dontwarn` (dangerous)

---

# 🎁 **13. Quick Summary (Super Simple)**

* **R8 = Android’s code shrinker + obfuscator**
* Enable using → `minifyEnabled true`
* Rules are written in → `proguard-rules.pro`
* Used to protect:

  * Models (Gson, Retrofit)
  * Libraries (FCM, Hilt, Room)
  * Reflection classes
* Makes APK smaller, harder to reverse engineer, faster
