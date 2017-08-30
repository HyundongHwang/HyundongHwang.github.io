---
layout: post
title: 170719 realm 놓치기 쉬운 부분 정리
---


# 170719 realm 놓치기 쉬운 부분 정리


<!-- TOC -->

- [170719 realm 놓치기 쉬운 부분 정리](#170719-realm-놓치기-쉬운-부분-정리)
- [스냅샷](#스냅샷)
- [or 질의](#or-질의)
- [in 질의](#in-질의)
- [연속질의](#연속질의)
- [집합 aggregation](#집합-aggregation)
- [realm 설정](#realm-설정)
- [기본 RealmConfiguration](#기본-realmconfiguration)
- [인 메모리 (In-memory) Realm](#인-메모리-in-memory-realm)
- [마이그레이션](#마이그레이션)
- [암호화](#암호화)
- [AsyncTask](#asynctask)
- [IntentService](#intentservice)
- [로그남기기](#로그남기기)

<!-- /TOC -->


<br>
<br>
<br>

realm을 프로젝트에 사용해 보고 있는데 튜토리얼만 보고는 놓치기 쉬운부분을 짜깁기 해 봤다.
https://realm.io/kr/docs/java/latest/
realm는 문서화를 참 잘 하는 것 같다.

<br>
<br>
<br>

# 스냅샷

모든 Realm 컬렉션은 라이브로 실시간 업데이트됩니다. 즉, 항상 최신 상태를 반영합니다. 대부분의 경우 이 방식이 바람직하지만, 원소를 수정하기 위해서 컬렉션을 순회하는 경우라면 어떨까요? 아래 예제와 같은 경우입니다.

```java
RealmResults<Person> guests = realm.where(Person.class).equalTo("invited", false).findAll();
realm.beginTransaction();
for (int i = 0; guests.size(); i++) {
    guests.get(i).setInvited(true);
}
realm.commitTransaction();
```

일반적으로 이 간단한 루프를 통해 모든 손님을 초대할 수 있으리라 기대합니다. 하지만 RealmResults가 즉시 업데이트되므로 손님 중 절반만 초대됩니다. 초대된 손님이 컬렉션에서 즉시 제거되고 모든 요소의 위치가 이동하기 때문에 i 매개변수가 증가하면 요소가 누락됩니다.

이를 방지하려면 컬렉션의 데이터를 스냅샷 으로 찍어야 합니다. 스냅샷은 요소가 삭제되거나 수정된 경우에도 요소의 순서를 보장해줍니다.

Realm 컬렉션에서 생성된 Iterator는 자동으로 스냅샷을 사용합니다. RealmResults와 RealmList에는 수동으로 스냅샷을 생성할 수 있는 createSnapshot()` 메서드가 있습니다.

```java
RealmResults<Person> guests = realm.where(Person.class).equalTo("invited", false).findAll();

// Use an iterator to invite all guests
realm.beginTransaction();
for (Person guest : guests) {
    guest.setInvited(true);
}
realm.commitTransaction();

// Use a snapshot to invite all guests
realm.beginTransaction();
OrderedRealmCollectionSnapshot<Person> guestsSnapshot = guests.createSnapshot();
for (int i = 0; guestsSnapshot.size(); i++) {
    guestsSnapshot.get(i).setInvited(true);
}
realm.commitTransaction();
```

<br>
<br>
<br>

# or 질의

```java
// Build the query looking at all users:
RealmQuery<User> query = realm.where(User.class);

// 질의 조건을 추가합니다
query.equalTo("name", "John");
query.or().equalTo("name", "Peter");

// 질의를 수행합니다
RealmResults<User> result1 = query.findAll();

// 같은 일들을 한번에 합니다 ("Fluent interface"):
RealmResults<User> result2 = realm.where(User.class)
                                  .equalTo("name", "John")
                                  .or()
                                  .equalTo("name", "Peter")
                                  .findAll();
```

<br>
<br>
<br>

# in 질의

```
RealmResult<User> r = realm.where(User.class)
                           .not()
                           .beginGroup()
                                .equalTo("name", "Peter")
                                .or()
                                .contains("name", "Jo")
                            .endGroup()
                            .findAll();
```

이런 쿼리에서는 in()을 사용하는 것이 훨씬 편리합니다.

```
RealmResult<User> r = realm.where(User.class)
                           .not()
                           .in("name", new String[]{"Peter", "Jo"})
                           finalAll();
```

<br>
<br>
<br>


# 연속질의


결과가 복사되지 않고 요청시에 바로 연산되기 때문에 데이터를 필터링하기 위해서 효율적으로 연속 질의를 사용하실 수 있습니다.

```
RealmResults<Person> teenagers = realm.where(Person.class).between("age", 13, 20).findAll();
Person firstJohn = teenagers.where().equalTo("name", "John").findFirst();
```

<br>
<br>
<br>

# 집합 aggregation

RealmResults는 다양한 집합(aggregation) 메소드를 가지고 있습니다.

```
RealmResults<User> results = realm.where(User.class).findAll();
long   sum     = results.sum("age").longValue();
long   min     = results.min("age").longValue();
long   max     = results.max("age").longValue();
double average = results.average("age");

long   matches = results.size();
```

<br>
<br>
<br>

# realm 설정

Realm을 어떻게 생성하는지 모든 측면을 제어하기 위해 RealmConfiguration 객체를 사용합니다. 최소한의 Realm 설정을 위해서는 아래의 것이 사용됩니다.

RealmConfiguration config = new RealmConfiguration.Builder().build();
위 설정은 Context.getFilesDir()에 위치한 default.realm 파일을 지정합니다.

전형적인 설정은 아래의 모습과 같습니다.

```
// RealmConfiguration은 빌더 패턴에 의해 생성됩니다.
// Realm 파일은 Context.getFilesDir()에 위치하면 이름은 "myrealm.realm"입니다.
RealmConfiguration config = new RealmConfiguration.Builder()
  .name("myrealm.realm")
  .encryptionKey(getKey())
  .schemaVersion(42)
  .modules(new MySchemaModule())
  .migration(new MyMigration())
  .build();
// 설정을 사용합니다.
Realm realm = Realm.getInstance(config);
```

또 여러개의 RealmConfiguration을 쓰는 것도 가능합니다. 이런 방법에서는 버전, 스키마, 파일위치 등을 Realm마다 독립적으로 제어할 수 있습니다.

```
RealmConfiguration myConfig = new RealmConfiguration.Builder()
  .name("myrealm.realm").
  .schemaVersion(2)
  .setModules(new MyCustomSchema())
  .build();

RealmConfiguration otherConfig = new RealmConfiguration.Builder()
  .name("otherrealm.realm")
  .schemaVersion(5)
  .setModules(new MyOtherSchema())
  .build();

Realm myRealm = Realm.getInstance(myConfig);
Realm otherRealm = Realm.getInstance(otherConfig);
```

<br>
<br>
<br>

# 기본 RealmConfiguration

RealmConfiguration를 기본 설정으로 저장할 수 있습니다. 커스텀 Application 클래스에 기본 설정을 넣고 다른 코드에서 사용가능한지 확인합니다.

```
public class MyApplication extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    // Context.getFilesDir()에 "default.realm"란 이름으로 Realm 파일이 위치한다
    Realm.init(this);
    RealmConfiguration config = new RealmConfiguration.Builder().build();
    Realm.setDefaultConfiguration(config);
```

<br>
<br>
<br>

# 인 메모리 (In-memory) Realm

비 영속적인 인 메모리 Realm 인스턴스를 정의해봅시다.

```
RealmConfiguration myConfig = new RealmConfiguration.Builder()
    .name("myrealm.realm")
    .inMemory()
    .build();
```

<br>
<br>
<br>

# 마이그레이션

마이그레이션
어떤 데이터베이스와 작업하든 모델 클래스(예를 들어 데이터베이스 스키마)는 항상 변경됩니다. Realm의 모델 클래스가 표준 객체로 정의되어 있어 RealmObject의 서브클래스에 상응하는 인터페이스를 변경하는 것은 쉽습니다.

예전 데이타베이스 스키마가 디스크에 저장한 데이터가 없다면 단지 정의를 바꾸는 것은 괜찮습니다. 이렇게 할 때 Realm은 코드에 정의된 코드와 디스크에 보이는 데이터 Realm을 비교하여 불일치가 있다면 예외를 던지게 됩니다. 이는 RealmConfiguration에 마이그레이션을 코드와 스키마 버전을 설정함으로서 해결할 수 있습니다.

```
RealmConfiguration config = new RealmConfiguration.Builder()
    .schemaVersion(2) // 스키마가 바뀌면 값을 올려야만 합니다
    .migration(new MyMigration()) // 예외 발생대신에 마이그레이션을 수행하기
    .build()
```

이를 이용하면 마이그레이션 코드는 필요시 자동으로 수행됩니다. 디스크의 스키마와 이전 버전 스키마를 위해 저장된 데이터를 갱신하는 여러 내장 메서드를 제공합니다.

```
// 새 클래스를 추가하는 마이그레이션 예제
RealmMigration migration = new RealmMigration() {
  @Override
  public void migrate(DynamicRealm realm, long oldVersion, long newVersion) {

     // DynamicRealm는 편집가능한 스키마를 노출합니다
     RealmSchema schema = realm.getSchema();

     // 버전 1로 마이그레이션: 클래스를 생성합니다
     if (oldVersion == 0) {
        schema.create("Person")
            .addField("name", String.class)
            .addField("age", int.class);
        oldVersion++;
     }

     // 버전 2로 마이그레이션: 기본 키를 넣고 객체를 참조합니다
     if (oldVersion == 1) {
        schema.get("Person")
            .addField("id", long.class, FieldAttribute.PRIMARY_KEY)
            .addRealmObjectField("favoriteDog", schema.get("Dog"))
            .addRealmListField("dogs", schema.get("Dog"));
        oldVersion++;
     }
  }
}
```

<br>
<br>
<br>

# 암호화

```
RealmConfiguration config = new RealmConfiguration.Builder()
  .encryptionKey(getKey())
  .build();

Realm realm = Realm.getInstance(config);
```

<br>
<br>
<br>

# AsyncTask

아래와 같이 doInBackground() 메서드에서 Realm을 열고 닫습니다.

```
private class DownloadOrders extends AsyncTask<Void, Void, Long> {
    protected Long doInBackground(Void... voids) {
        // 이제 백그라운드 스레드입니다.

        // Realm을 엽니다.
        Realm realm = Realm.getDefaultInstance();
        try {
            // Realm을 사용합니다.
            realm.createAllFromJson(Order.class, api.getNewOrders());
            Order firstOrder = realm.where(Order.class).findFirst();
            long orderId = firstOrder.getId(); // Id of order
            return orderId;
        } finally {
            realm.close();
        }
    }

    protected void onPostExecute(Long orderId) {
        // 안드로이드 메인스레드로 돌아왔습니다.
        // orderId를 가지고 order에 대해 Realm에 질의하거나
        // 어떤 연산을 수행합니다.
    }
}
```

<br>
<br>
<br>

# IntentService

아래와 같이 onHandleIntent() 메서드에서 Realm을 열고 닫습니다.

```
public class OrdersIntentService extends IntentService {
    public OrdersIntentService(String name) {
        super("OrdersIntentService");
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        // 이제 백그라운드 스레드입니다.

        // Realm을 엽니다.
        Realm realm = Realm.getDefaultInstance();
        try {
            // Work with Realm
            realm.createAllFromJson(Order.class, api.getNewOrders());
            Order firstOrder = realm.where(Order.class).findFirst();
            long orderId = firstOrder.getId(); // Id of order
        } finally {
            realm.close();
        }
    }
}
```

<br>
<br>
<br>

# 로그남기기

동기화된 Realm을 디버깅하는 것은 다루기 힘들기 때문에 로그를 남기는 것은 문제에 대해 이해를 높이기 위해 중요합니다. 세밀한 로그를 활성화하면 안드로이드 로그캣을 통해 무엇이 일어나는지 더 자세히 볼 수 있습니다.

```
RealmLog.add(new AndroidLogger(Log.VERBOSE));
```

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<script>
  (adsbygoogle = window.adsbygoogle || []).push({
    google_ad_client: "ca-pub-6928867874131068",
    enable_page_level_ads: true
  });
</script>

<!-- 라이브리 시티 설치 코드 -->
<div id="lv-container" data-id="city" data-uid="MTAyMC8yOTI4NC81ODUy">
 <script type="text/javascript">
   (function(d, s) {
       var j, e = d.getElementsByTagName(s)[0];

       if (typeof LivereTower === 'function') { return; }

       j = d.createElement(s);
       j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
       j.async = true;

       e.parentNode.insertBefore(j, e);
   })(document, 'script');
 </script>
<noscript> 라이브리 댓글 작성을 위해 JavaScript를 활성화 해주세요</noscript>
</div>
<!-- 시티 설치 코드 끝 -->