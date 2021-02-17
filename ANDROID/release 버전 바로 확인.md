## 플레이스토어 배포전에 release버전 미리 확인하기

프로젝트를 진행하며 debug로 컴파일하다가 배포할 때는 release버전으로 apk파일을 만든다.  
하지만 debug버전에서 문제가 없었을 지라도 release버전에서 문제가 생기는 경우가 있다.  

### 내가 마딱뜨린 문제  
+ 그래프를 그릴때 애니매이션효과를 주어 그래프가 그려지도록 하였지만 debug버전에서 잘 되던것이 release버전에선 동작하지 않음.

### 원인
```JAVA
   buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
```
build.gradle의 minifyEnabled true 때문에 애니메이션이 작동하지 않는다고 한다.

### 해결
priguard-rules.pro에 아래를 추가하였다.
```JAVA
-dontwarn com.github.mikephil.**
-keep class com.github.mikephil.charting.animation.ChartAnimator{*;}
```

### release버전으로 컴파일하여 배포전 미리 확인하는 방법
1. 배포시 필효한 Keystore 파일을 app 폴더안에 넣어준다.
2. build.gradle에 아래코드를 추가한다.
```JAVA
signingConfigs {
    release {
        storeFile file('test.keystore')
        storePassword "Your Password"
        keyAlias "Your Alias"
        keyPassword "Your Password"
    }
}
```
3. build.gradle에서 release{ } 안에 아래 코드를 추가한다.
```JAVA
signingConfig signingConfigs.release
```
4. Build Variants 에서 release를 선택후 Run을 하면 Keystore 정보가 들어간 apk파일을 디바이스로 컴파일 가능하다.
