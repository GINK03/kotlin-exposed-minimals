# Kotlin Exposed

KotlinのSQL言語をDSLとして利用できるライブラリ。  

便利で、DBと接続して何かやるときには、必須アイテムなのではいかと思う  


## gradle options 

**build**  
```console
$ ./gradlew build
```

**run** 
```console
$ ./gradlew run
```

**stand-alone jar**  
```console
$ ./gradlew jar
```

**stand-alone jarの実行**  
```console
$ kotlin -cp ./build/libs/kotlin-exposed-minimals.jar HelloWorldKt
```

## Kotlin Exposedプロジェクト
まだ、mavenなどの管理下になっていないので、[ここ](https://dl.bintray.com/kotlin/exposed/)から最新のjarを確認して、build.gradleに追加します  
```gradle
...
    classpath "org.jetbrains.exposed:exposed:0.10.1" // <- ここのバージョン
    classpath group: 'org.xerial', name: 'sqlite-jdbc', version: "3.21.0"
...
```
