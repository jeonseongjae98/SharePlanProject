# 모바일프로그래밍 7조
2021-2 모바일프로그래밍 팀프로젝트

## 팀원 소개
```
박세열
Role : LectureInfo, CreateLec1,2 액티비티 구현 보조, 정기적인 회의 리딩 및 프로젝트 플로우 구성, 프로젝트 주제 및 일정 관리
STUDENT NUMBER : 20172894
```

```
강경민
Role : 
STUDENT NUMBER : 
```

```
황재일
Role : LectureInfo 관련 액티비티 구현, 전체적인 코드 디버깅, 기능 개선 및 강화, 다른 팀원 도움
STUDENT NUMBER : 20203169
```

```
최은솔
Role : 
STUDENT NUMBER : 
```

```
전성재
Role : 
STUDENT NUMBER : 
```

## 프로젝트 소개
![image](https://user-images.githubusercontent.com/66048830/143769037-e1edcc30-697e-4495-8016-721700e1a9c9.png)\
강의별로 존재하는 일정들을 파악하기 쉽도록 진행한 프로젝트\
국민대학교 ecampus에 존재하는 달력 기능은, 강의들이 수강 마감 날짜가 아닌 수강 가능 기간에 모두 표시되어있어 매우 난잡함.\
특히 모바일에서는 일정 파악 자체가 쉽지 않아 각 강의를 일일이 확인하며 정확한 일정을 파악하여야 함.\
\
때문에, 언제 어디서나 각 강의의 일정 마감일을 정확하게 파악하고 관리할 수 있도록 하기 위해서 해당 프로젝트를 진행함.

<hr>

## 1. 개발 환경

* 운영체제 : Windows
* 개발 도구 : Android Studio 2020.3.1
* 개발 언어 : Java
* 데이터베이스 : Firebase
* AVD SDK : Pixel 2 API 30 (Android 11.0)

## 2. 기능 요약 설명
### 교수의 경우
* 강의 채널 생성 가능
* 강의 채널마다 학생들에게 공유되는 일정 추가 가능
### 학생의 경우
* 강의 검색 후 리스트에 등록 가능
### 공통
* 회원가입/로그인을 통한 계정 정보 저장 및 관리
* 강의 리스트에 강의 채널 등록 및 관리 가능
* 강의 채널별로 각 각의의 일정 마감일 확인 가능
* '모든 일정' 채널을 통해 등록한 모든 강의의 일정 통합 확인 가능

## 3. 데이터베이스 구조
### UserInfo
![image](https://user-images.githubusercontent.com/66048830/143769466-af007a97-6467-4a88-b524-ad4eeb9eb424.png)\
회원가입한 유저의 정보를 저장해두는 데이터베이스
* __Address__\
유저의 거주 주소
* __Authority__\
유저가 교수인지 학생인지를 나타내는 값.\
이 값에 따라 어플의 기능이 나누어진다.
* __Email__\
유저의 이메일 주소
* __Id__\
회원가입시 생성된 유저의 UID
* __Name__\
유저의 이름
* __Password__\
유저의 비밀번호
* __PhoneNumber__\
유저의 전화번호
* __StuNum__\
유저의 학번

<hr>

### LectureInfo
![image](https://user-images.githubusercontent.com/66048830/143769613-28ba458f-a6dd-4c37-88bc-74883c4ac89c.png)\
강의에 대한 정보를 저장해두는 데이터베이스\
N개의 강의가 0 ~ N-1까지의 UID로 저장된다.
* __Day__\
강의가 진행되는 요일
* __Division__\
강의의 분반
* __Name__\
강의의 이름
* __Professor__\
강의를 진행하는 교수님의 성함
* __Time__\
강의가 진행되는 시간

<hr>

### ClassInfo
![image](https://user-images.githubusercontent.com/66048830/143769696-9bfeeb79-b446-41ea-a171-4369f304e763.png)\
강의를 수강하는 학생들에 대한 정보를 저장해두는 데이터베이스\
<__"학번" : "이름"__>의 구조로 저장되어진다.

<hr>

### UserLectureInfo
![image](https://user-images.githubusercontent.com/66048830/143769785-d63a9dc3-d22c-4564-ad6b-f42e17ddafa4.png)\
유저별로 리스트에 등록해둔 강의들에 대한 정보를 저장해두는 데이터베이스\
<__"유저 UID" : "강의 UID"__>의 구조로 저장되어진다.

<hr>

### TodoInfo
![image](https://user-images.githubusercontent.com/66048830/143769878-b894f031-037f-4203-b44d-00160c9262af.png)\
강의별로 강의 채널에 등록된 강의의 일정에 대한 정보를 저장해두는 데이터베이스\
"강의 UID" → "일정의 마감 날짜" → "일정 데이터"의 구조로 저장되어진다.\
"일정 데이터"는 다음과 같은 구조로 저장되어진다.

* __Date__\
일정의 마감 날짜
* __RegisterNum__\
일정의 번호. 일정 데이터의 Key값과 같은 값을 가진다.
* __Title__\
일정의 자세한 내용
* __Type__\
일정의 간단한 분류.\
과제, 퀴즈, 강의, 시험 4가지로 분류된다.

<hr>

## 4. 기능 구현

### 박세열
### ClassListActivity.java
![image](https://user-images.githubusercontent.com/79959576/143796835-52bd9fdc-1eaa-45b5-9c71-1bf361c51e87.png)
* 학생이나 교수가 로그인을 하고 들어가면 보이는 강의 리스트
* 화면의 ListView에 UserLectureInfo 데이터베이스에 등록되어있는 정보들을 가공하여 표시해줌.
  * 데이터베이스 참조를 위해 DatabaseReference의 addListenerForSingleValueEvent를 이용함.
  * 교수의 경우 자신이 생성한 강의들을 보여주고 학생의 경우 자신이 SearchLecActivity에서 등록한 강의들을 
* 자신이 만들거나 수강하는 강의들이 리스트 형식으로 채널 구성
* ListView의 setOnItemClickListener를 이용하여 등록된 강의 채널을 터치하면 강의에 대한 ScheduleActivity로 넘어가게 됨
* 상단의 플러스 버튼을 통해 사용자의 Authority에 따라 CreateLec_1_Activity(교수), CreateLec_2_Activity(학생)으로 넘어가게됨  
<hr>

### 강경민

<hr>

### 황재일
### FindEmailActivity.java
![image](https://user-images.githubusercontent.com/66048830/143770929-621dcf06-09ba-4aec-babc-1fd4ea3efefe.png)
* 학번, 이름, 전화번호를 입력하고 "이메일 찾기" 버튼 터치 시, 회원가입시 사용한 이메일 주소를 확인할 수 있음.
  * Firebase의 UserInfo 데이터베이스를 참조하여 입력한 정보의 유저가 존재하는지 확인함.
  * 데이터베이스 참조를 위해 DatabaseReference의 addListenerForSingleValueEvent를 이용함.
  * 존재하는 유저일 경우 이메일 주소를 표시해줌.
  * 존재하지 않는 유저일 경우 잘못된 정보임을 Toast를 통해 표시해줌.
* 하단의 "로그인 화면으로 돌아가기" TextView 터치 시 LoginActivity로 돌아감.

### FindPwdActivity.java
![image](https://user-images.githubusercontent.com/66048830/143770972-59c0594e-3076-46df-b126-76d213117959.png)
* 학번, 이름, 전화번호, 이메일 주소를 입력하고 "비밀번호 찾기" 버튼 터치 시, 회원가입시 등록한 비밀번호를 확인할 수 있음.
  * Firebase의 UserInfo 데이터베이스를 참조하여 입력한 정보의 유저가 존재하는지 확인함.
  * 데이터베이스 참조를 위해 DatabaseReference의 addListenerForSingleValueEvent를 이용함.
  * 존재하는 유저일 경우 비밀번호를 표시해줌.
  * 존재하지 않는 유저일 경우 잘못된 정보임을 Toast를 통해 표시해줌.
* 비밀번호는 앞에서부터 8글자까지는 그냥 보여주고, 나머지 부분은 '\*'로 표시함.
  * __예시) PassWord***__
* 하단의 "로그인 화면으로 돌아가기" TextView 터치 시 LoginActivity로 돌아감.

### CreateLec_2_Activity.java
![image](https://user-images.githubusercontent.com/66048830/143771099-ef4c4d74-0e61-4851-a451-47c1ad613a94.png)
* 강의를 수강하는 학생들을 설정할 수 있음.
  * 가장 하단의 "추가" 버튼 터치 시 ClassInfo 데이터베이스에 해당 정보 등록
  * 여기서 등록된 학생들만 SearchLectureActivity를 통해 개인 리스트에 강의를 등록할 수 있음.
* 상단의 리스트뷰에서 등록하고자 하는 학생을 터치하여 강의에 등록할 수 있음.
  * ListView의 setOnItemClickListener를 이용하여 학생 정보 터치 시 하단의 리스트뷰에 등록한 학생의 정보를 표시해 줄 수 있음.
* 하단의 리스트뷰에는 현재 등록되어있는 학생들을 확인할 수 있음.
* "추가" 버튼 터치 시 LectureInfo 데이터베이스에 강의에 대한 정보를 등록함.
  * 강의의 UID를 항상 0 ~ N-1로 유지하기 위하여, ArrayList에 현재 LectureInfo에 등록되어 있는 강의 정보들을 모두 가져온 후, 신규 등록한 강의를 덧붙여서 Firebase에 재등록함.
  * 강의에 대한 정보는 CreateLecActivity에서 Intent를 통해 넘겨받음.
* LectureInfo와 ClassInfo에 등록을 완료한 후, finish()를 통해 ClassListActivity로 되돌아감.

### SearchLecActivity.java
![image](https://user-images.githubusercontent.com/66048830/143771573-9a276392-00c9-4d58-9b19-a765fb4a4dea.png)
* 화면의 ListView에 LectureInfo 데이터베이스에 등록되어있는 정보들을 가공하여 표시해줌.
  * 데이터베이스 참조를 위해 DatabaseReference의 addListenerForSingleValueEvent를 이용함.
  * onCreate() 실행 시, LectureInfo에 등록되어있는 모든 강의 데이터들을 가져와 ListView에 표시함.
* 상단 EditText에 찾고자 하는 강의의 이름을 입력한 후, "검색" 버튼을 터치 시 강의들을 검색하여 보여줌.
  * 데이터베이스 참조를 위해 DatabaseReference의 addListenerForSingleValueEvent를 이용함.
  * 입력한 문자열을 "포함"하는 모든 강의들을 가져와 ListView에 표시함.
  * 예시) "프로그래밍" 검색 시, "모바일프로그래밍", "C++프로그래밍", "객체지향프로그래밍" 등이 검색될 수 있음.
* ListView의 강의 item 터치 시 개인 리스트에 강의를 등록함.
  * ClassInfo를 조회하여, 현재 강의를 등록하고자 하는 유저가 이 강의의 ClassInfo에 등록된 학생인지 검사함(이 강의를 수강하는 학생인지).
  * ClassInfo에 등록되어 있지 않다면, Toast를 통해 강의를 수강하는 학생만 등록 가능함을 알려줌.
  * ClassInfo에 등록되어 있다면, UserLectureInfo를 조회하여 유저 개인 리스트에 이미 등록되어있는 강의인지를 검사함.
  * 이미 등록된 강의라면, Toast를 통해 이미 등록된 강의임을 알려줌.
  * 아니라면 유저 개인 리스트에 강의를 추가함.
  * 추가된 강의는 UserLectureInfo 데이터베이스를 갱신하여 저장함.
  * 유저별 강의 목차의 Index를 항상 0 ~ N-1로 유지하기 위하여 ArrayList에 현재 UserLectureInfo에 등록되어 있는 강의 정보들을 모두 가져온 후, 신규 등록한 강의를 덧붙여서 Firebase에 재등록함.

<hr>

### 최은솔

<hr>

### 전성재

<hr>
