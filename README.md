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

## updateする
```kotlin
Users.update({Users.id eq "alex"}) {
  it[name] = "Alexey"
}
```

## deleteする
```kotlin
Users.deleteWhere{Users.name like "%thing"}
```

## select(get)する
```kotlin
Users.select {Users.cityId.isNull()}.forEach {
   println("${it[Users.name]} lives")
}
```

## selectAllする
```kotlin
for (city in Cities.selectAll()) {
  println("${city[Cities.id]}: ${city[Cities.name]}")
}
```

## innerJoinする
これは、Table定義でどういう依存になっているか示されている必要があるっぽい
```kotlin
    (Users innerJoin Cities).slice(Users.name, Users.cityId, Cities.name).
            select {Cities.name.eq("St. Petersburg") or Users.cityId.isNull()}.forEach {
        if (it[Users.cityId] != null) {
            println("${it[Users.name]} lives in ${it[Cities.name]}")
        }
        else {
            println("${it[Users.name]} lives nowhere")
        }
    }
```

## groupByする
groupByして関数を掛ける必要が無いようでこれは、これで便利（これがあるから生SQL使わなくてもいいという面は多少ある）  
```kotlin
    ((Cities innerJoin Users).slice(Cities.name, Users.id.count()).selectAll().groupBy(Cities.name)).forEach {
        val cityName = it[Cities.name]
        val userCount = it[Users.id.count()]

        if (userCount > 0) {
            println("$userCount user(s) live(s) in $cityName")
        } else {
            println("Nobody lives in $cityName")
        }
    }
```

## gropする
```kotlin
drop (Users, Cities)
```

