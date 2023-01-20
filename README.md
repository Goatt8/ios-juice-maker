# 🥤 Juice Maker
## 프로젝트 개요
* 과일재고VC에서 과일재고값의 변경을 받아와 필요한 과일만큼을 소진해 juicemakerVC에서 과일주스를 만드는 프로젝트
* 프로젝트 인원: [Goat](), [Brody]()
* 프로젝트 기간: 2023.01.02 - 23.01.20

## 목차
- [타임라인](#타임라인)
- [순서도](#순서도)
- [클래스 다이어그램](#클래스-다이어그램)
- [기능 설명](#기능-설명)
- [트러블 슈팅](#트러블-슈팅)
- [키워드](#프로젝트를-통해-학습한-키워드)
- [팀회고](#팀회고)
- [참고 자료](#참고-자료)



## 폴더 구조

```
├── Controller
|   ├── JuiceMakeViewController
|   └── FruitStoreViewController
├── Model
|   ├── FruitStore
|   ├── JuiceMaker
|   └── Type
|   └── AlertBuilder
|   └── AlertDirector
└── View
    ├── Main
    └── Assets

```

----
    
## 타임라인
> 시간 순으로 프로젝트의 주요 진행 척도를 표시
> - 23.01.02(월): 참고자료 공식문서 읽기, STEP1 순서도 그리기
> - 23.01.03(화): STEP1 코드작성, STEP1 PR요청
> - 23.01.04(수): STEP1 진행속도 느릴 시 추가로 진행
> - 23.01.05(목): STEP2 진행 (재고 관리 viewcontroller까지 작업)
> - 23.01.06(금): STEP2 진행(과일 주스 재고 관리부분 완료)

> - 23.01.09(월): STEP2 PR 목표
> - 23.01.10(화): STEP2 PR 수정사항 반영
> - 23.01.11(수): 각자 진행
> - 23.01.12(목): STEP3 시작
> - 23.01.13(금): STEP3 PR요청

> - 23.01.16(월): STEP3 리뷰사항 반영 -> 프로젝트 머지
> - 23.01.17(화): -
> - 23.01.18(수): -
> - 23.01.19(목): 프로젝트 UML 클래스 다이어그램 작성
> - 23.01.20(금): 프로젝트 회고

## 순서도 
<img src="https://i.imgur.com/8ph8vjV.png" width="800" height="840"/> 

## 클래스 다이어그램
<img src="https://i.imgur.com/puNSp7t.jpg" width="800" height="740"/> 


<br/>


### 기능 설명
* #### 과일재고 화면
과일재고 화면입니다. 처음 실행했을 때 과일값이 10으로 설정돼있으며 (-), (+) Stepper를 통해 과일재고를 변경할 수 있습니다.

<img src="https://i.imgur.com/TrgGDet.png" width="400"/><br>
<br>

* `stepperTarget()`매서드를 통해 stepper의 값이 특정과일일 경우 stepperTarget값을 출력합니다.
* 출력된 target과일은 `stepperChanged()`매서드에서 싱글톤으로 불러온 `currentBasket[]`딕셔너리의 키값이 되고 스테퍼의 (+)(-)에 따라 재고값을 변경시킬 수 있었습니다.

--- 
* #### 과일재고 추가 및 감소 화면
과일재고stepper의 추가와 감소에 따라 정상적으로 재고값이 변경되는 모습입니다.

<img src="https://i.imgur.com/E0zrQdz.png" width="400"/>
<br>
* 싱글톤으로 연결된 `currenBasket[]` 값이 정상적으로 변경되고있는 모습입니다.

---
* #### 주스 제조성공 화면
주문할 주스에 필요한 과일수량이 충분하다면 과일수량을 감소하면서 주문에 성공하는 모습입니다. 성공했다는 alert이 표시됩니다

<img src="https://i.imgur.com/0rNCSVC.png" width="400"/>
<br>
* JuiceMaker모델 부분에서 `make()`매서드를 통해 과일수량이 충분하다면 `reduceList[]`에 담아 `reduceStock()`과일재고를 감소시켜주고줍니다
* `make()`매서드는 juiceMakeVC의 `orderButtonTapped()`매서드안에서 불러주며, 
    * 과일주스가 성공적으로 생성되고 과일이 차감되었다면, `showSuccessAlert()`를  
    * 과일값이 부족해 실패했다면 실패`showFailureAlert()`매서드와 `OutOfStockError`에러를 호출합니다.

---
* #### 주스 제조실패 화면
* 주문할 주수에 필요한 과일수량이 부족하다면 주문에 실패하는 모습입니다. 선택가능한 alert가 표시됩니다. '아니오'를 누르면 원래 화면으로 돌아오고 '네'를 누르면 재고수정화면으로 이동합니다.
<img src="https://i.imgur.com/j1Uv6Bm.png" width="400"/>

* showFailureAlert()매서드안에서 error의 종류 (`OutOfStockError`/ `UnknownError`)를 구분지어주고
* OutOfStock일시에 `alertDirctor.buildOutOfStockAlert`을 불러옵니다. AlertDirector안에는 alert의 경우에 따른 구현부가 작성되있고, `AlertDirector`는 `AlertBuilder`를 모델로 따르는 **builderPattern**을 구현해주었습니다.
* AlertBuilder의 `setAlertAction()`매서드의 클로저를 통해 AlertDirector안에 delegateCase가 true일때는 delegate로 받아온 `presentFruitStoreVC()`매서드가 작동하도록해서, alert이 "예"일때 재고관리 화면으로 넘어가도록 구현했습니다.


## 트러블 슈팅
 
#### 1. view의 stepper데이터를 기존FruitStore에 저장해둔(step1) 딕셔너리의 밸류값과 일치시키는 과정이 좀 문제가 많았습니다
* 과일들의 label 초기값이 10으로 시작해야함 -> FruitStore클래스를 전역으로(var currentFruitBasket) 받아와 setUpStepper함수를 통해 레이블과 딕셔너리의 밸류값을 일치시키는 작업을 진행해서 해결했습니다

```swift
//setUpStpper()
if let fruitValue = currentFruitBasket[.fruit]{
    fruitStepper.value = Double(fruitValue)
}
```

#### 2. label의 크기가, 숫자가 늘어남에 따라 같이 커지지않고 숫자가 일부 생략되는 오류가 생겨, setUpLabel()에 sizeToFit()기능을 구현해서 해결해주었습니다
> sizeToFit: 텍스트 크기에 맞게 라벨이 조정되는 UILabel 메서드
```swfit
fruitLabel.sizeToFit()
```

#### <수정> autolayout 적용 이후에 해당 메서드를 사용하지않아도 label에 오류가 생기지않아 sizoToFit() 메서드는 삭제했습니다.

#### 3. StepperChanged의 함수 크기
* stpperChanged함수가 sender에 UIStepper가 담겨서 실행되었을때 sender는 여러 개 stepper 중 하나의 stepper가 담길 수 있도록 하나에 함수에 여러개 Stepper 이벤트를 연결했습니다.
* 하지만 이로인해 함수 내부에서 switch case를 통해 분기별로 처리해줘야 하는 일이 생겼고 이는 결국 StepperChanged함수가 상당한 양의 코드를 갖게 만들었습니다.
* 이를 해결하기 위해 현재 sender로 들어온 Stepper가 어떤 과일에 대한 Stepper인지 알 수 있게 stepper를 파라미터로 넣으면 과일을 리턴해주는 함수를 만들었습니다.
```swift
 func stepperTarget(_ stepper: UIStepper)  -> Fruit? {
        switch stepper {
        case strawberryStepper:
            return Fruit.strawberry
        case bananaStepper:
            return Fruit.banana
        case pineappleStepper:
            return Fruit.pineapple
        case kiwiStepper:
            return Fruit.kiwi
        case mangoStepper:
            return Fruit.mango
        default:
            return nil
        }
    }
```
- 가져온 fruit을 기반으로 case별로 처리가 되었던 부분이 교체가 되어 많은 양의 코드를 제거할 수 있었습니다.
```swift
@IBAction func stepperChanged(_ sender: UIStepper) {
        guard let targetFruit = stepperTarget(sender) else {
            return
        }
        guard let fruitCount = currentFruitBasket[targetFruit] else {
            return
        }
        
        if Int(sender.value) > fruitCount {
            fruitStore.addOne(of: targetFruit)
            currentFruitBasket[targetFruit]! += 1
        } else {
            fruitStore.reduceOne(of: targetFruit)
            currentFruitBasket[targetFruit]! -= 1
        }
        
        let fruitLabel = fruitLabel(targetFruit)
        fruitLabel.text = currentFruitBasket[targetFruit]?.description
        fruitLabel.sizeToFit()
    }
```
#### 4. modalPresentationStyle
1. 과일재고view에서 데이터를 수정후 주스주문view로 돌아왔을때 수정된 데이터값이 싱글톤에는 담기지만 레이블에 표시되지않는 현상이 생겼습니다. 주스주문view의 viewWillAppear부분에 setUpLabel을 통해 레이블을 초기화시켜주는 함수를 구현해놓았는데, modalPresentationStyle이 **pageSheet형식으로 자동 적용되있어 주스주문view의 라이프사이클이 새로 돌지않는다는 사실을 발견**했습니다.
    * 이 문제는 **과일재고view 의 modalpresentationStyle을 .fullscreen형식**으로 바꾼결과 주스주문view의 라이프사이클이 돌면서 해결되었습니다.

#### 5. Navigation in Modal
>네비게이션 컨트롤러에 들어있는 viewController를 present했더니 navigation controller가 나타나지 않는 현상이 발생했습니다.
- Navigation도 결국 UIViewController를 상속받은 Container View Controller라는 것을 알았고 Navigation Controller내의 뷰 컨트롤러를 넣는 것이 아닌 Navigation Controller 자체를 모달에 넣음으로써 문제를 해결했습니다.


> 비슷한 종류의 alert를 만드는데 동일한 코드를 반복적으로 사용되고있다는 피드백을 받았고 이를 해결하기 위해 builder패턴을 적용했습니다.
- 빌더 패턴은 크게 client, director, builder 3가지 부분을 나누어서 사용합니다.
- client부분에서는 build를 지시하는 director와 직접 build하는 builder객체를 만들어서 디렉터에게는 특정 제품을 만들 수 있도록 지시하고 빌더에게는 만든 제품을 보여달라는 명령을 하는 코드를 작성합니다.
```swift
private func showSuccessAlert(with targetJuice: FruitJuice) {
    let builder = AlertBuilder()
    let alertDirector = AlertDirector()
    alertDirector.buildSuccessAlert(builder, with: targetJuice)

    self.present(builder.buildAlert(), animated: true)
}
```
- Director에서는 특정 순서로 생성 단계들을 실행하는 책임만 있습니다. 
```swift
final class AlertDirector {
    private var builder: Builder?
    
    func buildSuccessAlert(_ builder: Builder, with targetJuice: FruitJuice) {
        _ = builder
            .setTitle(AlertMessage.success)
            .setMessage("\(targetJuice.rawValue)" + AlertMessage.makeJuiceMessage)
            .setAlertAction(title: AlertMessage.yes, style: .default)
    }
}
```
- Builder부분은 Director에게 전달받은 명령들을 토대로 제품을 실제로 제작합니다. Builder프로토콜을 채택함으로써 title, message, alertAction을 구현하도록 만듭니다. AlertBuilder에서 buildAlert은 다른 메서드와 달리 Builder프로토콜 타입을 리턴하는 것이 아닌 직접 alert를 리턴을 하는데, 그 이유는 Director는 만드는데 명령만하는 책임이 있고 실제 제품을 개발하고 만든 제품을 리턴하는 것은 Builder의 책임이기 때문입니다. 
- 또한 만약 Builder프로토콜을 채택한 AlertBuilder와 AlertManual이 존재할 때 각각은 Builder객체가 아닌 Alert와 manual을 리턴해야 하기 때문에 구체적인 객체를 리턴해줘야 합니다.
```swift

protocol AlertDelegate {
    func presentFruitStoreVC()
}

protocol Builder {
    func setTitle(_ title: String) -> Builder
    func setMessage(_ message: String) -> Builder
    func setAlertAction(title: String, style: UIAlertAction.Style) -> Builder
}

final class AlertBuilder: Builder {
    
    var delegate: AlertDelegate?
    
    var alert = UIAlertController(title: "", message: "", preferredStyle: .alert)
    
    func setTitle(_ title: String) -> Builder {
        alert.title = title
        return self
    }
    
    func setMessage(_ message: String) -> Builder {
        alert.message = message
        return self
    }
    
    func setAlertAction(title: String, style: UIAlertAction.Style) -> Builder {
        let alertAction = UIAlertAction(
            title: title,
            style: style,
            handler: { _ in
                self.delegate?.presentFruitStoreVC()
        })
        alert.addAction(alertAction)
        return self
    }

    func buildAlert() -> UIAlertController {
        return alert
    }
}
```


#### 7. Delegate패턴
> Builder를 만들고 사용하다보니 depth가 깊어져 기존의 함수들에 접근하지 못하게 되었습니다. 이를 해결하기 위해 `특정 객체의 일을 다른 객체에게 위임하는 패턴인 델리게이트`를 사용하게 되었습니다.
- AlertBuilder내의 Action을 설정해주는 코드블록 안에서 juiceMakeViewController의 메서드인 presentFruitStoreVC에 접근해야 하는 상황이었습니다.
- 그래서 AlertBuilder내에 delegate프로퍼티를 설정해준 뒤 juiceMakeViewController에서 AlertDelegate를 채택한 후 AlerBuilder의 대리자를 자신으로 설정했습니다.
```swift
// juiceMakeViewController.swift
final class JuiceMakeViewController: UIViewController, AlertDelegate {
    private func showFailureAlert(_ error: juiceMakeError) {
        let builder = AlertBuilder()
        builder.delegate = self
        let alertDirector = AlertDirector()
        
        // something code...
    }
```
- builder의 setAlertAction함수에서는 delegate의 메서드를 completion에 넣음으로써 문제를 해결했습니다.
```swift
func setAlertAction(title: String, style: UIAlertAction.Style, delegateCase: Bool = false) -> Builder {
    let alertAction = UIAlertAction(
        title: title,
        style: style,
        handler: { _ in
            if delegateCase {
                self.delegate?.presentFruitStoreVC()
            }
    })
    alert.addAction(alertAction)
    return self
}
```



### 프로젝트를 통해 학습한 키워드
- `class`, `struct`
- `싱글톤`
- `Nested Types`, `enum`
- `dictionary`, `mapValues`
- `네이밍`
- `IBOutlet`, `IBAction`, `UIStepper`, `UILabel`
- `SizeToFit`, `auto layout`
- `modalPresentationStyle`, `Navigation Controller`
- `AlertController`



## 팀회고
<details>
    <summary>팀 회고 열기</summary>
    
### 우리팀이 잘한 점
* 프로젝트 진행중 네비게이터- 드라이버 역할분담을 지키려 노력했습니다.
* 피드백을 빠르게 수용하고 빌더패턴, 델리게이트와 같은 익숙치않고 새로운 개념들을 학습해 프로젝트에 적용했습니다.
* 의문에 대해 지나치지 않고 끝까지 해결하려고 노력했습니다.
### 우리팀이 개선할 점
* 빠르게 기능구현을 하고 리팩토링하는 형식으로 진행했는데 이 두 가지가 조화를 이루어 개발하는 것이 부족했습니다. 처음부터 완벽한 코드를 작성하기 보다는 자신이 쓰는 코드에 이유를 가지면서 기능구현에 초점을 맞추는 것이 중요하다고 느꼈습니다.
    
- Grouped Rules
    - 스크럼
        - 시작시간: 9:30
        - 식사시간: 1시간 30분
    - 계획
        - 기능구현에 초점을 둬서 완성하고 리팩토링을 반복
</details>




## 참고 자료
- [API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/)
- [Apple Developer Documentation - UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller)
- [Apple Archive - Auto Layout](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html)
- [Apple Developer Documentation - sizeToFit](https://developer.apple.com/documentation/uikit/uiview/1622630-sizetofit)
- [빌더 패턴](https://refactoring.guru/ko/design-patterns/builder)
