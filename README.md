# Android self study record

# 2025.01.08. 
아래의 medium 글을 읽고 쓴 글입니다.  
[2025년엔 어떤 local database를 사용해야 할까?](https://proandroiddev.com/which-local-database-should-you-choose-in-2025-comparing-realm-sqldelight-and-room-4221b354c899)
  
sharedPreferences, dataStore, SQLite, Room 을 다 사용해봤기 때문에, 위의 4개에 대한 비교 정리 글일 거라고 생각했는데 `Realm` 이라는 새로운 local database에 대해 설명하고 있어 흥미가 생겼습니다.


**Realm**은 MongoDB에서 제공하는 모바일 로컬 데이터베이스로, SQL 쿼리문을 사용하지 않는다는 것이 특징입니다. 따라서 쿼리문을 작성할 줄 몰라도 사용할 수 있습니다.    
따라서 중간에 데이터를 객체에 mapping 하는 과정이 없고, 바로 객체에 접근하기 때문에 빠릅니다.
이러한 특징 때문에 작동 구조가 firebase의 firestore과 비슷하다고 느꼈습니다.   
> ‼ 다만, APK의 용량이 늘어난다는 점은 단점이 될 수 있습니다.

가장 두드러지는 특징은 `데이터 값이 바뀌면, 바로 view를 업데이트할 수 있다는 점입니다.` 

Realm은 값이 변화를 감지합니다.  
```kotlin
open class Dog(
  val name : String,
  val age : Int
) : RealmObject()
```
위와 같은 RealmObject가 있다고 가정합시다. 
```kotlin
// open database
val configuration = RealmConfiguration.create(schema = setOf(Dog::class)) 
val realm = Realm.open(configuration)

// put data
val dog = Dog().apply{ name = "puppy"; age = 2}

realm.writeBlocking {
  val putDog = copyToRealm(dog)
}
```
Dog 객체에 데이터를 1개 추가했습니다. 
이런 일이 있을 때 `addChangeListener`를 통해 변화를 감지할 수 있습니다.
```kotlin
val allData = realm.where(Dog::class.java).findAll()
allData.addChangeListener { result, _ ->
  println(result)
}
```
addChangeListener가 변화를 감지하면, UI 업데이트를 지시할 수도 있습니다.

이런 이유 때문에, Realm이 빠르고, UI 업데이트가 간편하다고 합니다.
