# 몽고DB 기초 명령어

## 데이터베이스 명령어

```bash
# 데이터베이스 사용
use database

# 데이터베이스 목록 출력
show dbs

# 현재 사용중인 데이터베이스 출력
db

# 현재 사용중인 데이터베이스 정보 출력
db.stats()

# 데이터베이스 삭제
db.dropDatabase()
```

## 컬렉션 명령어

 - `컬렉션 생성`
    - createCollection: 컬렉션 생성
        - capped: 고정된 크기를 가진 컬렉션을 생성한다. 사이즈 초과시 가장 오래된 데이터를 덮어쓴다.
        - size: 해당 컬렉션의 최대 사이즈 (capped 설정시 사용)
        - max: 해당 컬렉션에 추가할 수 있는 최대 도큐먼트 수
    - show collections: 컬렉션 조회
    - drop: 컬렉션 삭제
```javascript
// 컬렉션 생성
db.createCollection("book")

// 컬렉션 생성
db.createCollection(
    "book",
    {
        capped:true, 
        size:6142800, 
        max:10000
    }
)

// 컬렉션 조회
show collections

// 컬렉션 삭제
db.book.drop()
```

## 도큐먼트 명령어

### 도큐먼트 삽입(데이터 등록)

 - insert: 단일 또는 다수의 도큐먼트를 삽입
 - insertOne: 단일 도큐먼트 삽입
 - insertMany: N개의 도큐먼트 삽입
```javascript
// insert()
db.book.insert({
    "name": "어린왕자"
})

db.book.insert([
    {"name": "어린왕자"},
    {"name": "홍길동전"}
])

// insertOne
db.book.insertOne({
    "name": "어린왕자"
})

// insertMany
db.book.insertMany([
    {"name": "어린왕자"},
    {"name": "홍길동전"}
])
```

### 도큐먼트 삭제(데이터 삭제)

 - remove: 몽고DB 3.0 이전버전에서 도큐먼트 삭제시 사용. 호환성을 위해 여전히 사용 가능하지만 deleteOne(), deleteMany() 사용 권장
 - deleteOne: 필터와 일치하는 첫 번째 도큐먼트 삭제
 - deleteMany: 필터와 일치하는 모든 도큐먼트 삭제
```javascript
// remove: 도큐먼트 전체 삭제
db.book.remove()

// remove: name이 어린왕자인 도큐먼트 삭제
db.book.remove({"name": "어린왕자"})

// deleteOne: name이 어린왕자인 도큐먼트 1개 삭제
db.book.deleteOne({"name": "어린왕자"})

// deleteMany: name이 어린왕자인 모든 도큐먼트 삭제
db.book.deleteMany({"name": "어린왕자"})
```

### 도큐먼트 갱신(데이터 수정)

 - `replaceOne`
    - 도큐먼트를 완전히 새로운 것으로 치환한다.
```javascript
// name이 Slime인 도큐먼트를 새롭게 치환한다.
db.monsters.replaceOne({
    name: "Slime"
}, {
    "hp": 50,
    "damage": 10,
    "defense": 5,
    "level": 5
})
```

 - `update & updateOne & updateMany`
    - update: 필터와 일치하는 도큐먼트 갱신
    - updateOne: 필터와 일치하는 첫 번째 도큐먼트 갱신
    - updateMany: 필터와 일치하는 모든 도큐먼트 갱신
    - $ 제한자를 사용하지 않는 경우 치환된다. 제한자를 이용하면 특정 필드만 변경이 가능하다.
        - $set: 수정 제한자
        - $inc: 증감 제한자 (int, long, double, decimal 타입만 가능)
        - $push: 배열 값 추가
            - $each: 한 번에 여러개의 배열 값 추가
            - $slice: 배열이 특정 크기 이상으로 늘어나지 않게 top N 목록 처리
        - $pull: 배열 값 제거 (조건에 일치)
        - $pop: 배열 값 제거 (배열의 양쪽 요소 제거)
```javascript
// name이 Slime인 도큐먼트의 정보 변경
db.monsters.update({
    "name": "Slime"
}, {
    $set: {
        "damage": 8,     // damage를 8로 변경
        "tribe": "SLIME" // tribe 필드를 추가
    }
})

// name이 Slime인 도큐먼트의 정보 변경
// upsert 옵션으로 수정할 대상이 없을 때 insert 수행
db.monsters.update({
    "name": "Slime"
}, {
    $set: {
        "damage": 8
    }
}, {
    upsert: true
})

// name이 Slime인 도큐먼트의 정보 변경
// update는 기본적으로 1개의 행만 업데이트한다. multi 옵션을 주면 모든 도큐먼트 업데이트
db.monsters.update({
    "name": "Slime"
}, {
    $set: {
        "damage": 8
    }
}, {
    multi: true
})

// $inc: $set과 비슷하지만 숫자를 증감하기 위해 설계
// name이 pinball인 도큐먼트의 score를 10 증가
db.games.update({
    "name": "pinball"
}, {
    "$inc": {
        "score": 10
    }
})

// $push: 배열의 값 추가
db.book.update({
    "name": "어린왕자"
}, {
    "$push": {
        "category": "humanity"
    }
})

// $push + $each
db.book.update({
    "name": "어린왕자"
}, {
    "$push": {
        "category": {
            "$each": ["humanity", "novel"]
        }
    }
})

// $push + $each + $slice
db.movies.updateOne({
    "genre": "horror"
}, {
    "$push": {
        "top10": {
            "$each": ["Nightmare on Elm Street", "Saw"],
            "$slice": -10
        }
    }
})

// $pull
// lists의 모든 도큐먼트의 todo 배열 요소 중 laundry 제거
db.lists.updateOne({}, {
    "$pull": {
        "todo": "laundry"
    }
})
```

 - `findAndModify & findOneAndXxx`
    - findAndModify: 몽고DB 3.2 이전에서 사용. 삭제, 대체, 갱신 가능
    - findOneAndDelete: 조회 + 삭제
    - findOneAndReplace: 조회 + 치환
    - findOneAndUpdate: 조회 + 수정
```javascript
// findAndModify
// query: 필터 조건
// update: 수정 작업
// new: 작업 이전 반환인지, 작업 이후 반환인지
db.monsters.findAndModify({ 
    query: {
        name: 'Demon'
    }, 
    update: {
        $set: {
            damage: 150
        }
    }, 
    new: true 
});

// findOneAndUpdate
// returnNewDocument 옵션으로 갱신 후 도큐먼트를 반환
db.monsters.findOneAndUpdate(
    {
        "name": "Demon"
    }, 
    {
        "$set": {
            "damage": 150 
        }
    }, 
    {
        "sort": {
            "priority": -1
        },
        "returnNewDocument": true
    }
);
```

### 도큐먼트 조회

 - `find & findOne`
    - 첫 매개변수에서 필터 조건을 정의하고, 두 번째 매개변수로 조회할 필드를 정의한다.
```javascript
// users 컬렉션의 모든 도큐먼트 조회
db.users.find()
db.users.find().pretty()
db.users.find({})

// age가 20인 도큐먼트 조회
db.users.find(
    {
        age: 20
    }
)
```

 - `쿼리 조건절`
    - $lt, $lte, $gt, $gte를 사용해 특정 범위 내 값을 쿼리할 수 있고, 키 값이 특정 값과 일치하거나 일치하지 않는 지는 $ne, #eq 를 사용할 수 있다.
```javascript
{ 필드: { $gt: 값 } } // 필드 > 값
{ 필드: { $lt: 값 } } // 필드 < 값
{ 필드: { $gte: 값 } } // 필드 >= 값
{ 필드: { $lte: 값 } } // 필드 <= 값

{ 필드: { $eq: 값 } } // 필드 == 값
{ 필드: { $ne: 값 } } // 필드 != 값

{ 필드: { $in: [ 값1, 값2, 값3, ... ] } // 필드 == (값1 or 값2 or 값3)
{ 필드: { $nin: [ 값1, 값2, 값3, ... ] } // 필드 != (값1 and 값2 and 값3)

// 조건1 or 조건2
{ $or: [ { 조건1 }, { 조건2 }, ... ] } 

// 조건이 간단하면 그냥 { 필드: 값, 필드: 값 } 이렇게 $and가 없어도 되지만, 여러 논리연산자를 겹쳐 쓸 경우 $and가 필요합니다.
// (조건1 or 조건2) and (조건3 or 조건4)
{ $and: [
  { $or: [{ 조건1 }, { 조건2 }] },
  { $or: [{ 조건3 }, { 조건4 }] }
] }

// 조건1, 조건2 ... 모두 만족하지 않는 다큐먼트
{ $nor: [{ 조건1 }, { 조건2 }, ...] } 

// 조건이 아닌값.  $nor의 단일 버전
{ $not: { 조건 } }
```

 - `null`
    - null은 값이 null인 경우와, 존재하지 않음과 일치한다.
    - 만약, 도큐먼트에 키가 존재하고 값이 null인 도큐먼트만을 찾고 싶다면 $exists 조건절을 이용한다.
```javascript
// 예시 데이터
{ "_id": ObjectId(".."), "name": "짱구", "age": 5}
{ "_id": ObjectId(".."), "name": "철수", "age": 5}
{ "_id": ObjectId(".."), "name": "맹구", "age": null}

// age가 null인 맹구가 조회된다.
db.users.find({age: null})

// 모든 도큐먼트가 조회된다. (모든 도큐먼트에 hobby라는 키가 존재하지 않는다.)
db.users.find({hobby: null})

// 조회되는 도큐먼트가 없다.
db.users.find(
    {
        hobby: {
            $eq: null,
            $exists: true
        }
    }
)
```
