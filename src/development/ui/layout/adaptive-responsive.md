---
title: 반응형 및 적응형 앱 만들기
description: 크기와 방향 변화에 반응하도록 모바일, 웹 앱을 만드는 것은 중요합니다.
short-title: 반응형 및 적응형
---

Flutter의 주요 목표는 어떠한 플랫폼에서든 
하나의 코드 베이스로 앱을 개발할 수 있는 프레임워크를 만드는 것입니다.

이것은 앱이 시계, 두 개의 화면을 가진 폴더블 폰, 
높은 해상도를 가진 모니터와 같이 다양한 사이즈의 스크린에 출력된다는 것을 의미합니다.

이 시나리오의 개념을 설명하는 두 가지 용어는 _적응형_과 _반응형_입니다.
이상적으로, 앱이 두 가지를 모두 충족하길 바랄 것입니다.
하지만, 정확하게 이 용어들이 의미하는 바가 무엇일까요?
이 용어들은 비슷하지만, 같지 않습니다.

## 적응형 앱과 반응형 앱의 차이점

_적응형_, _반응형_이라는 용어는 서로 앱의 다른 차원처럼 느껴질 수 있습니다:
반응형 앱이 아닌 적응형 앱을 만들거나, 반대의 상황도 마찬가지입니다.
그리고 당연히 둘을 모두 충족할 수도 있고 아닐 수도 있습니다.

**반응형**
:  보통 _반응형_ 앱은 사용 가능한 화면 크기에 맞게 레이아웃을 조절합니다.
   예를 들면, 사용자가 윈도우의 크기를 변경시켜 UI를 재배치 시킨다던가 
   디바이스의 방향을 바꾸는 것을 말합니다. 
   이런 기능은 특히 동일한 앱이 시계, 전화, 태블릿에서 랩톱 또는 
   데스크톱 컴퓨터에 이르기까지 다양한 장치에서 실행될 수 있는 경우에 필요합니다.

**적응형**
: 모바일 및 데스크톱과 같은 다양한 디바이스 유형에서 실행되도록
  앱을 _조정_하려면 마우스 및 키보드 입력과 터치 입력을 처리해야 합니다.
  이것은 앱의 시각적 밀도, 계단식 메뉴나 하단 시트 같은 컴포넌트 선택 작동 방식, 
  top-level 윈도우 같은 플랫폼별 기능 사용 등에 대한 
  기대치가 디바이스마다 다르다는 것을 의미합니다.

## 반응형 Flutter 앱 만들기

Flutter로 디바이스의 화면 크기와 방향을 스스로 조절하는 앱을 만들 수 있습니다.

반응형 디자인으로 Flutter 앱을 만드는 2가지 기본 접근법이 있습니다:

**[`LayoutBuilder`][] 클래스를 사용하세요**
: LayoutBuilder의 [`builder`][] 프로퍼티로부터,
  [`BoxConstrains`][] 객체를 얻을 수 있습니다.
  어떤 화면을 보여줄지 제약조건의 프로퍼티들을 평가해서 결정하세요.
  예를 들어, 만약 [`maxWidth`][]가 너비의 구분점보다 크다면,
  왼쪽에 리스트를 가지고 있는 행과 함께 [`Scaffold`][] 객체를 반환하고,
  더 좁다면, 리스트를 포함한 드로어를 가진 [`Scaffold`][] 객체를 반환합니다.
  디바이스의 높이, 종횡비 또는 다른 속성들을 사용하여 화면을 조절하는 것 또한 가능합니다.
  사용자가 폰을 회전한다던가 Nougat의 타일 앱을 넣는 것처럼
  제약조건이 변화할 때, 빌드 함수가 작동합니다.

**Build 함수에서 [`MediaQuery.of()`][] 메서드를 사용하세요**
: [`MediaQuery.of()`][] 메서드는 현재 앱의 크기, 방향 등의 정보를 제공합니다.
  이 메서드를 사용하는 것은 특정 위젯의 크기가 아닌
  전체 컨텍스트를 기반으로 결정을 내리려는 경우에 더 유용합니다.
  다시 말하지만, 이 함수를 사용 사용자가 앱의 크기를 변경하면 빌드 함수가 자동으로 실행됩니다.

반응형 UI를 만들 때 유용한 기타 위젯과 클래스들이 존재합니다:

* [`AspectRatio`][]
* [`CustomSingleChildLayout`][]
* [`CustomMultiChildLayout`][]
* [`FittedBox`][]
* [`FractionallySizedBox`][]
* [`LayoutBuilder`][]
* [`MediaQuery`][]
* [`MediaQueryData`][]
* [`OrientationBuilder`][]

### 기타 리소스

더 자세한 내용을 원한다면, Flutter 커뮤니티의 
기여를 포함한 다음과 같은 리소스들을 참고하세요:

* [Flutter에서 여러 화면 크기와 방향에 맞춰 개발하기][] by Deven Joshi
* [Flutter에서 반응형 UI를 빌드하세요][] by Raouf Rahiche
* [반응형 크로스 플랫폼 Flutter 랜딩 페이지 만들기][]
  by Priyanka Tyagi
* [어떻게 다른 화면 크기에 따라 flutter 앱이 반응하도록 만들 수 있나요?][], StackOverflow 질문


[`AspectRatio`]: {{site.api}}/flutter/widgets/AspectRatio-class.html
[`BoxConstraints`]: {{site.api}}/flutter/rendering/BoxConstraints-class.html
[Flutter에서 반응형 UI를 빌드하세요]: {{site.medium}}/flutter-community/build-responsive-uis-in-flutter-fd450bd59158
[`builder`]: {{site.api}}/flutter/widgets/LayoutBuilder/builder.html
[`CustomMultiChildLayout`]: {{site.api}}/flutter/widgets/CustomMultiChildLayout-class.html
[`CustomSingleChildLayout`]: {{site.api}}/flutter/widgets/CustomSingleChildLayout-class.html
[Flutter에서 여러 화면 크기와 방향에 맞춰 개발하기]: {{site.medium}}/flutter-community/developing-for-multiple-screen-sizes-and-orientations-in-flutter-fragments-in-flutter-a4c51b849434
[`FittedBox`]: {{site.api}}/flutter/widgets/FittedBox-class.html

[`FractionallySizedBox`]: {{site.api}}/flutter/widgets/FractionallySizedBox-class.html
[어떻게 다른 화면 크기에 따라 flutter 앱이 반응하도록 만들 수 있나요?]: {{site.so}}/questions/49704497/how-to-make-flutter-app-responsive-according-to-different-screen-size
[`LayoutBuilder`]: {{site.api}}/flutter/widgets/LayoutBuilder-class.html
[Making Cross-platform Flutter Landing Page Responsive]: {{site.medium}}/flutter-community/making-cross-platform-flutter-landing-page-responsive-7fffe0655970
[`maxWidth`]: {{site.api}}/flutter/rendering/BoxConstraints/maxWidth.html
[`MediaQuery`]: {{site.api}}/flutter/widgets/MediaQuery-class.html
[`MediaQuery.of()`]: {{site.api}}/flutter/widgets/MediaQuery/of.html
[`MediaQueryData`]: {{site.api}}/flutter/widgets/MediaQueryData-class.html
[`OrientationBuilder`]: {{site.api}}/flutter/widgets/OrientationBuilder-class.html
[`Scaffold`]: {{site.api}}/flutter/material/Scaffold-class.html

## 적응형 Flutter 앱 만들기

gskinner 팀이 작성한 [적응형 앱 빌드 하기][]을 통해 적응형 Flutter 앱을
만드는 방법에 대해 배워보세요.

다음의 The Boring Show 에피소드들도 참고하세요:

<iframe style="max-width: 100%" width="560" height="315" src="{{site.youtube-site}}/embed/n6Awpg1MO6M" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
[적응형 레이아웃][]

<iframe style="max-width: 100%" width="560" height="315" src="{{site.youtube-site}}/embed/eikOZzfc0l4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
[적응형 레이아웃, 파트 2][]

적응형 앱의 훌륭한 예시를 보고 싶다면,
gskinner와 Flutter 팀이 협업으로 제작한
스크랩북 만들기 앱인 Flutter Folio를 참고하세요:

<iframe width="560" height="315" src="{{site.youtube-site}}/embed/yytBENOnF0w" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

[Folio 소스코드][]는 GitHub에서 확인이 가능합니다.
[gskinner blog][]에서 더 많은 정보를 습득하세요.

### 기타 리소스

다음과 같은 리소스들을 통해 플랫폼 적응형 앱을 만드는 방법을 학습할 수 있습니다:

* 본 사이트의 페이지, [플랫폼별 동작 및 적응][].
* 금년도 FlutterViking 컨퍼런스에서 발표한 Aloïs Deniel의 블로그 포스트와 비디오, [진정한 적응형 유저 인터페이스 디자인하기][].
* [Flutter gallery app][] ([repo][])은 적응형 앱으로 구현되었습니다.

[적응형 레이아웃]: {{site.youtube-site}}/watch?v=n6Awpg1MO6M&t=694s
[적응형 레이아웃, 파트 2]: {{site.youtube-site}}/watch?v=eikOZzfc0l4&t=11s
[적응형 앱 빌드하기]: {{site.url}}/development/ui/layout/building-adaptive-apps

[진정한 적응형 유저 인터페이스 디자인하기]: https://aloisdeniel.com/#/posts/adaptative-ui
[Flutter gallery app]: {{site.gallery}}
[Folio 소스코드]: {{site.github}}/gskinnerTeam/flutter-folio
[gskinner blog]: https://blog.gskinner.com/
[플랫폼별 동작 및 적응]: {{site.url}}/resources/platform-adaptations
[repo]: {{site.repo.gallery}}
