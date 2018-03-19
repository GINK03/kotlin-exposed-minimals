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

# Kotlin Exposed
## Tableの定義
Tableというのを継承させた静的なオブジェクトに要素の定義を行います　　
例)  
```kotlin
object Users : Table() {
    val id = varchar("id", 10).primaryKey() // Column<String>
    val name = varchar("name", length = 50) // Column<String>
    val cityId = (integer("city_id") references Cities.id).nullable() // Column<Int?>
}
```

## Databaseの接続  
JDBCで接続できるDBなら大抵利用できる  
```kotlin
  Database.connect("jdbc:sqlite:./db.sqlite", driver = "org.sqlite.JDBC")
```

## transaction block
transactionと呼ばれるブロック内に、DSLの記述を行います  
```kotlin
  transaction(transactionIsolation = Connection.TRANSACTION_SERIALIZABLE, repetitionAttempts = 3) {
    create (Cities, Users)

    val saintPetersburgId = Cities.insert {
        it[name] = "St. Petersburg"
    } get Cities.id

    val munichId = Cities.insert {
        it[name] = "Munich"
    } get Cities.id
    ....
```

## tableを作る
transaction内部でcreateを呼び出します
```kotlin
create (Cities, Users)
```

## insertする
カラム名はブラケットの内部の変数名になります  
```kotlin
Users.insert {
  it[id] = "andrey"
  it[name] = "Andrey"
  it[cityId] = saintPetersburgId
}
```
