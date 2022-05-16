# Cross-Browsing Report

[더티바이](https://dirtybye.com/)

# 0. Browser Test

![Untitled](https://user-images.githubusercontent.com/52296323/168630047-7ce2ea0d-42ac-438c-8025-b53aead3fe35.png)

브라우저 시장 점유율 기준으로 Chrome, IE, Edge, Firefox, Safari, Opera 총 6개의 브라우저에서 테스팅을 진행해보았다.

## Result

- Chrome : no problem
  ![Untitled 1](https://user-images.githubusercontent.com/52296323/168630085-85e73da7-0849-48ab-80da-d32d1deb2921.png)
- IE : .scroll-notify icon position problem, banner scroll event not working, .product-desc animation not working
  ![Untitled 2](https://user-images.githubusercontent.com/52296323/168630104-54089eab-42ed-432f-8ca1-6a855d9282ee.png)
- Edge : no problem
  ![Untitled 3](https://user-images.githubusercontent.com/52296323/168630121-b2d09738-da02-4e44-8fcc-c6f82d66d327.png)
- Firefox : When end banner-scroll, #wrap { overflow-y : scroll } not working
  ![Untitled 4](https://user-images.githubusercontent.com/52296323/168630146-cea2633e-5c07-41a2-9582-4e214e9dc217.png)
- Safari : no problem
  ![Untitled 5](https://user-images.githubusercontent.com/52296323/168630168-8b1cbef2-b22c-4877-8f8a-5fd359993bb6.png)
- Opera : no problem
  ![Untitled 6](https://user-images.githubusercontent.com/52296323/168630182-9d12b588-e00f-4ef8-98a8-c8893d343843.png)

# 1. Problem

## 1. .scroll-notify icon positioning (IE)

![Untitled 7](https://user-images.githubusercontent.com/52296323/168630199-3db9f00f-e1fe-4459-81f0-bdc885267040.png)

check browser CSS attribute

- IE Browser 는 모든 버전이 { position: sticky; }, CSS 속성을 지원하지 않는다.

## 2. banner scroll event not working (IE)

![Untitled 8](https://user-images.githubusercontent.com/52296323/168630226-6ad3fd21-611b-49ce-a321-01c4290329d3.png)

IE test logging [1]

- 브라우저 스크롤에 맞춰서 배너의 X축을 이동 시킬때 사용했던 JavaScript 함수 중, ScrollTo를 IE 에서는 지원하지 않는다.

## 3. .product-desc animation not working (IE)

![Untitled 9](https://user-images.githubusercontent.com/52296323/168630242-ecc307c0-8bc6-4f03-9a89-34de5d518918.png)

chrome test logging [1]

![Untitled 10](https://user-images.githubusercontent.com/52296323/168630253-231c5bc6-e958-4c48-ae00-1dbf06714c99.png)

firefox test logging [1]

![Untitled 11](https://user-images.githubusercontent.com/52296323/168630288-f4859d4c-1a20-467f-8a17-f6a21e8837e3.png)

opera test logging [1]

![Untitled 12](https://user-images.githubusercontent.com/52296323/168630302-02fd7b49-b9f3-4d59-918f-4f8a06da541c.png)

IE test logging [2]

- 컴포넌트의 위치 정보를 쉽게 파악할 수 있도록 JavaScript에서 제공해주는 DOM API 중 getBoundingClientRect() 함수가 있는데, 다른 브라우저에서는 DOMRect라는 객체를 반환하는 반면, IE는 ClientRect라는 객체를 반환했고, 이 객체의 프로퍼티 구조는 DOMRect의 프로퍼티 구조와 약간의 차이를 보였다.

## 4. #wrap { overflow-y : scroll } not working (FireFox)

![Untitled 13](https://user-images.githubusercontent.com/52296323/168630318-92489018-8bb5-4716-8675-984cb776566b.png)

firefox test logging [2]

- 배너를 다 보면 #wrap의 Scroll이 활성화 되어 footer 까지 내려가지는 로직을 구성했는데, 이 때 올라오는 과정에서 다시 배너를 다 보고 올릴 수 있도록 lock을 걸기 위한 변수 wrapLockBack에 잠금이 걸렸던 #wrap의 scrollY 값을 할당 시켰었다.

![Untitled 14](https://user-images.githubusercontent.com/52296323/168630336-1d93a5d5-d6b2-4f01-b8b6-ecb8bc751555.png)

firefox test logging [3]

![Untitled 15](https://user-images.githubusercontent.com/52296323/168630354-9508592b-695a-4b28-834d-2d176ddddc6e.png)

chrome test logging [2]

- 위에 로직에서 쓰인 값들의 상태를 확인하기 위해 Chrome과 Firefox에서 #wrap 컴포넌트의 scrollY값을 Global하게 찍어서 비교를 해보았다. Firefox는 다른 브라우저에 비해 스크롤 감도가 낮은걸로 결론을 내렸다. 아무리 강하게 스크롤을 내려도 Firefox의 #wrap scroll은 scroll을 잠그기 위해 사용하는 wrapLockBack 변수의 값을 넘기지 못했다.
- 이 때문에 #wrap { overflow-y : scroll } 은 자꾸만 풀리게 됐고, 이로인해 footer 쪽 까지 스크롤이 되지 않았다.

# 2. Solution

## 1. .scroll-notify icon positioning (IE)

- 1차적으로 sticky-polyfill.js 를 적용 시켜보았었다. 하지만, 애니메이션이 많은 페이지 특성 상 해당 polyfill은 원하는대로 작동하지 않았고, 많은 지연을 주었다.
- 때문에 css적으로 풀어내면 베스트이겠다. 라는 생각을 했고, 해당 페이지의 레이아웃의 배너 라인쪽은 1080px로 고정되어있는 상태, 즉 sticky대신 fixed를 사용해서도 충분히 제작이 가능했던 페이지였다.
- 기존의 footer 컴포넌트의 뒤쪽으로 들어가는 느낌을 살리기 위해 footer 컴포넌트의 포지션 속성에 relative를 추가하여, z-index가 유효해지도록 만들었고, 이를 통해 active 상태가 됐을 때, scroll-notify icon이 뒤쪽으로 빨려들어가는 느낌을 살린상태로, 해당 문제를 해결했다.

```css
.scroll-notify {
  position: fixed;
  top: calc(1080px - 128px);
  left: 50%;
  /* (...) */
}

#footer {
  z-index: 999;
  position: relative;
}
```

### [ 추가적인 문제점 ]

- IE에는 z-index 색인 버그가 존재하는데, 부드러운 스크롤 기능이 기본적으로 켜져있는 IE로 인해서 scroll-notify와 footer가 겹치는 시점에서 스크롤후 페이지를 렌더링하는 과정에서 심각하게 떨림 현상이 발생했다. 그래서 그냥 IE에서 발생하는 해당 문제점은 답이 없다라고 판단하고, 해당 아이콘이 안뜨는 방향으로 문제를 회피했다.

```jsx
var agent = navigator.userAgent.toLowerCase();
if (
  (navigator.appName == "Netscape" && agent.indexOf("trident") != -1) ||
  agent.indexOf("msie") != -1
) {
  $scrollNotify.style.display = "none";
}
```

## 2. banner scroll event not working (IE) - scroll-polyfill.js

- 크로스브라우징을 해결하는 가장 빠른 방법은 polyfill을 제작하거나 구해서 브라우저에 적용하는 방법이다. 다행히 IE에서 제공하지 않는 스크롤을 편하게 이용할 수 있는 객체들은 누군가가 polyfill을 제작해놔서, 설치 후 서버에 스크립트를 추가하는 방법으로 해결할 수 있었다.
- npm i element-scroll-polyfill
  ![Untitled 16](https://user-images.githubusercontent.com/52296323/168630380-fe4dddb5-3394-400e-abc1-25f931fef36e.png)
  ```html
  <script src="/web/v2/js/polyfill.js"></script>
  ```

## 3. .product-desc animation not working (IE)

- 기존의 코드는 getBoudingClientRect().x 좌표값으로 조회를 했다. 하지만 IE에서 반환되는 ClientRect 객체는 x값을 가지고 있지 않아서, IE에서 동작을 안했던 것이었다.
- 다행히 ClientRect와 DOMRect 객체 사이에는 left라는 해당 컴포넌트에 먹혀있는 절대값 포지션을 조회할 수 있는 공통분모의 프로퍼티가 존재했다.

```jsx
const product1_X = $productDesc1.getBoundingClientRect().left;
const product2_X = $productDesc2.getBoundingClientRect().left;
const product3_X = $productDesc3.getBoundingClientRect().left;
const product4_X = $productDesc4.getBoundingClientRect().left;
```

## 4. #wrap { overflow-y : scroll } not working (FireFox)

- #wrap의 스크롤을 잠그기 위해서 사용됐던, wrapLockBack 변수의 소수점을 없애는 로직을 누어서 낮은 스크롤 감도를 가지고 있는 FireFox에 대응했다.

```jsx
if (boxScrollLeft + window.innerHeight < 1682) {
  wrapLockBack = Math.floor((boxScrollLeft * wrapHeight) / 5635.26);
  $wrap.scroll({
    top: (boxScrollLeft * wrapHeight) / 5635.26,
  });
}
```

## 5. other

### 1. display : flex

![Untitled 17](https://user-images.githubusercontent.com/52296323/168630409-b0381961-e275-4326-b762-b5180eb7b1a1.png)

check browser CSS attribute

- 가장 우려했던 상황은 flex가 적용된 레이아웃의 붕괴였는데, 다행히 완전 옛날 버전의 브라우저만 사용하지 않으면 flex 속성은 대부분의 브라우저에서 지원이 되는 것 같다.

### 2. Browser Scrollbar Hidden

```css
*::-webkit-scrollbar {
  /* Chrome, Safari, Opera 대응 */
  display: none;
}

* {
  /* IE, Edge 대응 */
  -ms-overflow-style: none;
}

*::-moz-scrollbar {
  /* Firefox 대응 - 1 */
  display: none;
}

* {
  /* Firefox 대응 - 2 */
  scrollbar-width: none;
}
```

### 3. 추가적으로 발생할 수 있는 문제점

1. ie에서 배너를 모두 보고, 푸터까지 스크롤 하고나서 위쪽으로 스크롤 시, 배너를 다 보면 다시 헤더까지 올라갈 수 있는데, 이 때 배너의 위치에서 잠기도록 사용한 wrapLockBack 변수에서 일시적인 지연이 발생한다. 여기서 잠깐의 깜박임이 존재한다. 내 노트북 성능이 안 좋은 건지 모든 ie에서 이러한 점이 발생하는지는 모르겠지만, 크리티컬한 느낌은 아니라 그냥 놔두었다.
