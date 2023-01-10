# 1일차

## 팀빌딩

앱 릴리즈까지 하루 이틀 남은 상황, 모두가 시간이 촉박해 보인다. 대부분의 팀원들이 핵심 기능 자체는 구현을 한 상황이지만

신경 쓰지 못한 세밀한 부분이나, 이슈 사항들이 있는 것 같다.

나도 마찬 가지며 오늘은 그런 것들 위주로 진행 해야겠다.

## 개인학습

앱 아이콘을 (드디어) 넣었다. 무료 상용 아이콘중에 이쁜 책장 아이콘이 있길래 이것으로 했다.

또한 런치스크린도 추가 했다. 다만 원래 앱이름도 넣을려고 했는데 폰트가 적용이 안되어서 다른 방법을 시도하다가

stack over flow에서 "런치 스크린은 아주 간단한 이미지면 충분합니다." 라는 말을 듣고 그냥 앱 아이콘과 같은 이미지만 1초정도 보여주는 것으로 끝냈다.

또한 메인 뷰의 컬렉션뷰 내용이 없을 시 책을 추가 해달라는 안내 라벨을 넣은 것 처럼

책 검색 테이블 뷰 2가지에 모두 적용 시켰다.

백업/복구 사용 테스트를 해보니 부족했던 것이 눈에 들어와서 몇가지 디테잃한 부분을 넣었다.

1. 백업 시 프로그래스바 추가
2. 너무 큰 백업/복구 모달을 작게 축소
3. 복구 시 잘못된 파일에 대한 예외처리
4. 예외처리에 얼럿 메시지 추가

완성도를 올리는 시간이라 생각해서 사용 테스트 및 수정을 위주로 한 날인 것 같다.

내일은 드디어 릴리즈를 위한 수업을 듣고 아마도 릴리즈를 할 것 같다.

하 제발 리젝안났으면

# 2일차

## 팀빌딩

앱스토어 커넥트에서 테스터로 팀원들을 등록하고 테스트플라이트로 서로 앱을 사용해보는 시간을 가졌다.

팀원 분들이 생각보다 하위버전이나 여러기종 테스트를 하시진 못한것 같다.

나도 몇가지 버그를 찾아서 알려드렸는데 뭔가 도움이 된 것 같아 뿌듯..

내앱도 테스트를 해주셨는진 모르겠는데 아직까지 별 이야기는 없으신거 보니

딱히 큰 이슈 사항은 없으신 것 같다.(나름 혼자 치밀하게 테스트함)

## 개인 학습

어제 자기 전 이것 저것 만지작 하다보니 몇 가지 버그가 나와서 수정하였다.

컬렉션 내용을 읽지않음 으로 변경 하고 팝하여 메인화면으로 돌아와도 컬렉션이 읽음탭에 

눌렀을 때 크래시 되는 현상이 있었는데, 다행히 수정은 금방되었다.(willDisappear 로 간단하게 수정완료)

앱스토어 심사 과정에 대해 유튜브 블로그등 다양하게 살펴보았다.

역시 나는 문서를 작성하는 것에 약해서 머리가 아프다.

오늘 밤 ~ 새벽에 작성완료를 목표로 하고는 있는데, 일단 빠르게 제출 하는게 좋을지

준비만 해놓고 내일 제출 할지 아직 결정하지는 못했다.