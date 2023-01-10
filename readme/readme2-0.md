# 1일차

## 팀빌딩

오늘도 어김없이 각자 프로젝트 진행도를 공유했다. 나는 어제 구현한 APIManager 등을 보여주며, 이슈등을 정리 하였다.

똑똑한 팀원이 내가 구현한 코드에서 Codable을 사용하면 코드를 많이 줄일 수 있고, 사용법도 간편하다고 추천해 주셨다.

그래서 어제 만든 코드에 Codable을 적용시켜 보려고한다.

팀원은 쉽다고 하였으나, 나는 swift자체 이해도가 부족하기 때문에 순탄하지 않을 예정(..?) 이다.

## 개인학습

Codable이 라이브러리 인줄 알았는데, 기본 탑재되어 있는 기능 이었다.(혼자 git 찾고 있었음)

swift 형식의 데이터를 json 형태로 encoding 하거나 decoding 하는 기능이다.

json <-> swift 구조체(클래스)

이를 이용하기 위해선 json의 키 값과 1대1 매칭되는 변수가 존재해야 한다.

나의 경우는 응답받은 json을 decode 하는 작업에 이용 해야 한다.

일단 사용하는 kakao book api가 응답해주는 json형식을 이용해서 구조체를 만들어야 하는데, 아주 좋은 툴이 있었다.

https://app.quicktype.io/

여기 들어가서 insomnia에서 임의의 값으로 호출 후 받은 응답 json 형식을 기입하면

짜잔~ 하고 만들어준다.

```swift
// MARK: - Welcome
struct BookInfo: Codable {
    let documents: [Document]
    let meta: Meta
}

// MARK: - Document
struct Document: Codable {
    let authors: [String]
    let contents, datetime, isbn: String
    let price: Int
    let publisher: String
    let salePrice: Int
    let thumbnail: String
    let title: String
    let translators: [String]
    let url: String

    enum CodingKeys: String, CodingKey {
        case authors, contents, datetime, isbn, price, publisher
        case salePrice = "sale_price"
        case thumbnail, title, translators, url
    }
}

// MARK: - Meta
struct Meta: Codable {
    let isEnd: Bool
    let pageableCount, totalCount: Int

    enum CodingKeys: String, CodingKey {
        case isEnd = "is_end"
        case pageableCount = "pageable_count"
        case totalCount = "total_count"
    }
```

이후 Alamofire를 이용하여 value값을 받는 것 까진 똑같지만

Alamofire의 응답 값은 Any형이기 때문에 data형으로 변환이 필요하다.

그 다음 디코딩!

```swift
let decoder = JSONDecoder()
let jsonData = try JSONSerialization.data(withJSONObject: value, options: .prettyPrinted)
let parsingData = try decoder.decode(BookInfo.self, from: jsonData)
```               

이제 응답 받은 json 데이터가 BookInfo에 파싱되어 들어가게 된다.

그리고 BookInfo의 데이터를 테이블뷰와 연결된 bookData 배열에 추가해주면

끝!

```swift
self.endData = json.meta.isEnd
for item in json.documents {
    self.bookData.append(item)
}
```

정말 딱 3~4줄이면 끝난다. swiftyJSON도 나쁘지 않다고 생각했는데, 정말 간결하다.

다시 보면 정말 별거 없는데, 첨에 머리속에 구조가 안들어와서 애먹었다.

예시 코드도 뒤죽박죽이라 여기저기 많이 검색해 본 것 같다. 역시나 예상대로 쉬운건 없다ㅠ

여기다 시간을 생각보다 많이써서 realm에 필요한 기본 코드들 몇개 뿐이 구현을 하지 못했다.

내일까지로 계획되어 있는데 어쩌지..?ㅋㅋㅋ


# 2일차

## 팀빌딩

다들 본격적으로 구현에 들어간 느낌. 팀원 모두 이건 이렇게 저건 이렇게 해야지 하고 구현 전에 다 머리속에 정리해 놓은 것 같다.

진도가 확 나간 팀원도 있고, 천천히 다지기? 중인 팀원도 보인다. 난 이도저도 아닌것 같은데ㅋㅋ

어제 codable에 대해 가르쳐준 팀원 선생님(?) 에게 어제 내가 구현한 코드를 보여 드렸는데 먼가 부끄러웠다.

사실 안보여주고 얼렁뚱땅 넘어 가려 했는데 보자고 하셔서 당황. 그래도 별말 없으신거 보면 나쁘지 않았던 거겠지..?

끝에 팀원들에게 별표를 이용해서 평점보여주는 라이브러리에 대해 아시냐고 물어봤는데, 갑자기 30초도 안되서 찾으셨다!!

https://github.com/evgenyneu/Cosmos

Star 도 2만개가 넘기 때문에 사용해도 괜찮을 것 같다.

혹시 다른 라이브러리도 있나 찾아보구 사용해 봐야겠다.

## 개인 학습

오늘 부터 CRUD 단계에 돌입했다. 대충 스키마를 만들어 봤다.

```swift
    @Persisted var title: String // 제목
    @Persisted var authors: String // 글쓴이
    @Persisted var publisher: String // 출판사
    @Persisted var content: String? // 내용
    @Persisted var myReview: String? // 나의 리뷰
    @Persisted var myScore: Float? // 나의 평점
    @Persisted var bookCover: String // 책표지 url
    @Persisted var isbn: String // 책 등록번호
    @Persisted var read: Bool // 읽음,안읽음
    
    //PK(필수) : Int, String, UUID, ObjectID -> AutoIncrement
    @Persisted(primaryKey: true) var _id: ObjectId
```

나중에 해보면서 느낀게 myReview 같은 경우 옵셔널로 주어서, 데이터 초기화 시 nil 값이 들어가게 되는데,

이렇게 되니 nil일때와 텍스트뷰가 빈값 일때 조건을 다 처리해주어야 해서 코드가 길어지게 되었다.

```swift
//nil 값 혹은 "" 비어있는 값인지 확인 후 placeholder를 넣어줌
        if let myReview = myBookDetail.myReview {
            if myReview.isEmpty {
                reviewTextView.text = "내용을 입력해주세요"
                reviewTextView.textColor = UIColor.lightGray
            } else{
                reviewTextView.text = myReview
                reviewTextView.textColor = UIColor.black
            }
        } else{
            reviewTextView.text = "내용을 입력해주세요"
            reviewTextView.textColor = UIColor.lightGray
        }
```

코드 보완 기간에 수정 해보면 좋을 것 같다.

그리고 화면 전환 시 선택된 cell의 모든 realm 데이터를 보낼지 아니면 primaryKey 정보만 보내고

전환된 화면에서 새로 데이터를 불러 올지 고민 했는데, 결론적으론 후자의 방법을 선택했다.

나의 경우 책을 추가 후 추가된 데이터와 함께 -> 책 상세보기 화면으로 이동해야할 경우가 생길 수도 있는데,

이렇게 되면 후자의 방법으로 하는게 편해 보인 것이 그 이유 이다.(사실 나도 잘 모른다)

아!! 오늘 가장 오래 걸렸던 것이 있다.

책커버 이미지를 저장하는 문제였다. 처음에는 책을 추가 할 시, url을 이용하여 이미지를 다운받아 document 폴더에 저장하고

데이터베이스에 저장된 이미지파일의 url을 저장하려고 했다. 근데 생각보다 복잡해서 고민하던 중(예전 백업과 유사하지만 그래도 어렵던..)

kingfisher를 이용하는 좋은 방법이 없나 찾아보게 되었다.

찾아 보니 kingfisher의 캐싱을 이용하는 방법들이 에제로 많이 올라와 있었다.



방식은 이렇다

책 추가 -> kingfisher로 이미지 캐싱, key값은 image url로 저장

그리고 데이터베이스에 그냥 api에서 받은 이미지url값을 넣어놓는다.

책 불러오기 -> url 값을 key로 이미지캐시값을 불러와 뷰에 띄운다

```swift
//이미지 캐싱
let downLoader = ImageDownloader.default
let imageURL = URL(string: self.bookData[row].thumbnail)!

downLoader.downloadImage(with: imageURL, completionHandler:  { result in
        switch result {
            case .success(let value):
                ImageCache.default.storeToDisk(value.originalData, forKey: bookCover)

            case .failure(let error):
                print(error)
            }
    })
```
```swift
//이미지를 불러오기
extension UIImageView {
    func setBookCover(with coverImageKey: String) {
        let cache = ImageCache.default
        cache.retrieveImage(forKey: coverImageKey, options: nil) { result in // 캐시에서 키를 통해 이미지를 가져온다.
            switch result {
            case .success(let value):
                if let image = value.image { // 만약 캐시에 이미지가 존재한다면
                    self.image = image // 바로 이미지를 셋한다.
                } else {
                    guard let url = URL(string: coverImageKey) else { return }
                    let resource = ImageResource(downloadURL: url, cacheKey: coverImageKey) // URL로부터 이미지를 다운받고 String 타입의 URL을 캐시키로 지정하고
                    self.kf.setImage(with: resource) // 이미지를 셋한다.
                }
            case .failure(let error):
                print(error)
            }
        }
    }
}
```
DB에 저장된 url값을 cache key 혹은 download url 두가지 모두 사용하는 방법이다.

생각 보다 아주 간단한 원리! 속도도 빠른 것 같고, 오프라인일 경우 약간? 의 대처도 가능해 보인다. 

기본적으로 캐시에 이미지가 있나 확인 후 없으면 url을 이용해 새로 이미지를 다운로드 하기 때문이다.

물론 구현이 엄청 오래 걸린건 비밀이다.(간단하지만 간단하지 않은 뭐 그런)

# 3일차

## 팀빌딩

어김없이 찾아온 팀빌딩 시간, 발표를 진행 하였다.

나는 어제 진행한 kingfisher에 관하여 공유 하였는데, 팀원에 의하면 캐싱은 자동적으로 해준다고 한다ㅠㅠ

끝나고 더 찾아봐야할지, 시간이 얼마 남지 않았으니 다른 것 부터 진행 할지 모르겠으나 내 성격상 전자일듯..

오늘은 따로 더 언급 할 내용은 없고, 큰 문제 없이 다들 잘 진행중 인 것 같다.

## 개인 학습

kingfisher 라이브러리에 대해 더 자세히 찾아보았다.

팀원분이 말씀 하신 대로 기본적으로 kf.setImage 가 호출되면 다운로드 받고 캐시메모리에 적재하게 된다.

즉, 한번 받아왔던 데이터는 다시 url을 이용해 다운로드를 호출 하더라도 캐시메모리에 존재한다면 다운받지 않고 사용한다.

오늘 좀 더 자세히 찾아보게 되었는데, kingfisher 캐시 저장 방식은 메모리에 적재 / 디스크에 적재 2가지 방법이 있고 기본적으론 둘다 저장한다.

옵션을 이용해 메모리만 사용 할 수도 있고, timeout 시간도 정해 놓을 수 있었다.

그러다 캐시디스크 경로를 들어가보니 600개가 넘는 사진파일이 쌓여있었다..ㄷㄷ 나도 모르게 이미 캐시된 이미지를 사용하고 있었던 것.

어떻게 수정 할까 고민 하다가 책 추가 화면의 이미지는 메모리에만 적재하는 방식을 사용하고, 추가를 하게 되면 추가될 사진은 timeout 없는 디스크에 저장 하기로 하였다.

이렇게 하면 굳이 검색 내용의 이미지까지 디스크에 적재하 불필요한 용량을 막을 수 있을 거라 생각한다.

```swift
//디스크에 저장하지 않고, 메모리에만 적재한다!
cell.bookCoverImage.kf.setImage(with: url,placeholder: UIImage(named: "noimg"), options: [.cacheMemoryOnly])
```

얼추 정리 된거 같아서 어제 팀원이 가르쳐 주신 Cosmos 라이브러리로 별 평점 view? 를 구현했다.

alert을 띄워서 설정 하려 했는데 라이브러리에서 별을 눌러 값이 변경되는 시점에 Double값을 반환 해주길래

변경 될때 마다 별이 있는 특정 cell만 업데이트 시켜주었다. 방법도 간단하고 세상엔 금손이 참 많다.

그리고 오늘은 구현이 50퍼센트 정도 되었다고 생각한 날이다.

"절반 까지 왔으니 쉬어가자" 는 아니고 앱을 테스트해보며 신경쓰지 못한 디테일한 부분과 앱 간이테스트를 진행했다.

나중에 해야지 하고 놓고 있었던 디자인등을 수정하고, 검색과 CRUD(사실 delete는 아직 구현이 안되었다ㅎㅎ..) 테스트를 이것 저것 넣어서 해보았다.

폰트좀 바꾸고, 컬렉션뷰에 그림자효과좀 주고, view에 border도 주고 하니까 먼가 허접하지만 그럴싸해 보인다

# 4일차

## 팀빌딩

수업시간에 잭님이 하신 말씀이 있다. 이 화면 저 화면 이거 했다 저거 했다 하면 하나도 제대로 안될 수 있으니, 한 화면이라도 확실히 하고 넘어가라!!!

이말을 듣고 뜨끔했다ㅎㅎ.. 나도 산만하게 여기저기 쑤시지 말고 하나만 제대로 공략 해야겠다.

프로젝트 기간이 어느정도 지나서 그런지 다들 지쳐보인다. 나도 아주 피곤한 상태 오늘은 약간만 쉬어가는 날을 해야겠다.

미리 미리 눈에보이는 버그를 잡아가며 구현하고 있으니까 속도가 더디긴 하지만 나에겐 주말이있다!

팀원이 UIMenu 라는 것을 가르쳐 주셔서 삭제에 적용시켜 보고 싶은데 이게 14버전 이상만 지원해서 고민을좀 해봐야겠다.

## 개인 학습

오늘은 계속 뒤로 미루던 책을 길게눌러 삭제하는 기능을 구현 하였다.

찾아보니 좋은 예시들이 많아 입맛대로 수정하여 잘 적용 하였다.

원래 빠르게 하고 오늘은 휴식을 취할까 했는데 하..또 이상한 이슈를 발생해서 늦은 새벽까지 붙잡고 있게 되었다.

분명 건드린적 없는 상단 segment control의 반응 속도가 눈에 띄게 느려졌다.

이전 커밋을 적용시켜 테스트 해보니 문제가 없었고, 커밋내용 하나하나 읽어가며 어디서 문제가 생겼다 몇시간을 뒤져본 결과

```swift
func finishLongPressWhenTappedBackground() {
        let tapEvent = UITapGestureRecognizer(target: self, action: #selector(finishLongPress))
        tapEvent.cancelsTouchesInView = false
        //더블 제스처
        tapEvent.numberOfTapsRequired = 2
        view.addGestureRecognizer(tapEvent)
    }
```

long press gesture 을 끝내는 기능이 원인!!!!!!!!!

이 기능이 무엇이냐면 컬렉션셀을 길게 눌르면 delete가 가능한 상태로 가게 되는데

view를 연속으로 타닥 터치하게 되면 완료 됬다고 판단하여 원래 상태로 돌아가게 하는 기능이다.

아마 예상이지만 main view 자체에 제스처를 등록해 두어서 상속받는 세그먼트컨트롤에도 영향이 간 것 같다.

그래서 수정은?

```swift
func finishLongPressWhenTappedBackground() {
        let tapEvent = UITapGestureRecognizer(target: self, action: #selector(finishLongPress))
        tapEvent.cancelsTouchesInView = false
        //더블 제스처
        tapEvent.numberOfTapsRequired = 2
        bookCollectionView.addGestureRecognizer(tapEvent)
    }
```

bookCollectionView에만 제스처를 달아두었더니 깔끔하게 해결!

collectionView 가 아닌 곳을 누르게  반응하지 않게 된건 아쉬우나, 사용자 입장에서 큰 문제는 아닌것 같다.

# 5일차

## 팀빌딩

오늘은 먼저 자신의 개인앱을 만들어본 새싹멤버들의 발표가 있었던 날이었다. 모두 발표를 잘해주셔서 경청하게 되었다.

다들 자기만의 이유가 존재하여 앱 제작을 시작하였다고 하는데, 나는 그러한 이유나 목적이 투명하지 않은 것 같다ㅠ

가장 크게 느꼈던 점은 정말정말 앱 기획 단계가 중요하다는 점.. 난 너무 부족한 것 같다.

팀원 발표에서 눈에 띈점은 갑자기 확확 구현이 진행된 분들이다.

먼가 원기옥? 처럼 자료조사를 마친 후 한번에 구현이 되신 듯한? ㅋㅋ

이번주엔 계획대로 라면 QA 및 백업복구를 구현해야 한다. 

여기에 몇가지 추가 하자면 만들면서 추가하면 좋겠다 라고 생각 된 디테일한 부분(테이블 뷰 내용이 없을 시 뷰에 텍스트를 넣어 준다던가)

다만, 핵심 기능은 구현해 놓았기 때문에 약간 휴식도좀 취하면서 할 예정이다.

## 개인 학습

나의 책장 검색 기능을 구현 하였다. 새로운 책을 추가하는 것과 달리 로컬db에서 불러오기 때문에

검색 버튼 없이 실시간으로 검색 결과를 보여주는식으로 구현 하였다.(모두 수업에서 배운 것 감사합니다!!)

또 어제 만들었던 longpress 기능에 햅틱반응을 추가 했다. 길게 누르면 띠딩~ 하면서 진동비슷한 반응이 오게 된다.

simulator가 아닌 내 아이폰으로 테스트를 해보니 이상한 문제가 발생 했다.(iOS 14.6)

실시간으로 검색 결과를 바꾸는 것 까진 좋은데, 검색결과를 클릭하게 되면 서치바의 포커싱이 풀리면서 검색내용이 사라졌다.

열심히 구글링 한 결과 아래와 같은 코드가 필요하다는 사실을 알았다!!

```swift
searchController.obscuresBackgroundDuringPresentation = false
```

검색 결과가 보여 질때 배경을 가리는? 그런 기능인데 false처리 해주니 원하는 대로 기능이 구현 되었다.

앞으로도 추가된 기능은 테스트/수정 과 병행하고,

추가하면 좋을 것들을 생각한 뒤 간단하게 디테일한 부분을 살릴 수 있다면 추가 해봐야겠다.

# 6일차

## 휴식 하는날

# 7일차

## 개인 학습

오픈 라이센스 목록과 백업 복구 기능을 추가 하였다.

수업때 배운 코드를 응용하였는데, 이거 없었으면 어떻게 만들었을까? 할 정도로 복잡했다.

흐름 자체는 어렵다고 보기 어려운데, 경로 설정이 워낙 많다보니 정말 해깔린다.

경로를 모두 한번 씩 출력해 보았는데, 그걸로도 이해하기엔 부족하다.

추후 파일 경로쪽을 다시 혼자 공부해 봐야 할 것 같다.
