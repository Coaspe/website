---
title: Android 개발자를 위한 Flutter
description: Android 개발 지식을 적용하여 Flutter 앱을 만드는 방법에 대해 알아보십시오.
---

이 문서는 기존 Android 지식을 활용하여 
Flutter 모바일 앱을 개발하고자 하는 Android 개발자를 대상으로 합니다.
Android 프레임워크의 기본 내용을 이해하고 있다면,
이 문서를 활용하여 Flutter 개발로 도약할 수 있습니다.

Flutter가 다양한 기능과 구성을 위해 모바일 운영체제를 사용하기 때문에 
Android 관련 지식과 기술은 Flutter 앱을 만들 때 매우 유용합니다.
Flutter는 모바일에서 UI를 만드는 새로운 방법이지만,
UI 이외 작업을 위해, Android (그리고 iOS)와 통신하는 플러그인 시스템도 가지고 있습니다.
Android의 전문가라면, Flutter를 사용하기 위해 모든 것을 다시 배울 필요가 없습니다.

필요한 부분에 가장 적합한 질문을 찾아내는 방식으로 이 문서를 요리책(cookbook)처럼 활용하실 수도 있습니다.

## 뷰

### Flutter에서 `View`와 동일한 것은?

{{site.alert.secondary}}
react-style 프로그래밍(또는 선언적 프로그래밍)이 기존 명령형 스타일과 어떻게 다를까요? 
비교를 위해, [선언적 UI 소개](/docs/get-started/flutter-for/declarative)를 참조하세요.
{{site.alert.end}}

Android에서, `뷰`는 화면에 나타나는 모든 것의 기반입니다. 
버튼, 툴바, 입력창 등 모든 것이 뷰입니다.
Flutter에서는 `위젯`이 `뷰`와 유사합니다. 
위젯이 Android의 뷰와 정확하게 일치하는 건 아니지만, 
Flutter를 익힐 때 위젯이 "UI를 선언하고 구성하는 방식"이라고 이해할 수 있습니다.  

하지만, 위젯은 `뷰`와 조금 차이가 있습니다. 먼저, 생명주기(lifespan)가 다릅니다.
위젯은 변경 불가능하며 변경이 필요할 때까지만 존재합니다. 
위젯 혹은 위젯의 상태가 변경되면. Flutter는 위젯 인스턴스의 새로운 트리를 생성합니다.
반면, Android의 뷰는 한 번만 그려지고, `invalidate`가 호출되기 전까지는 다시 그리지 않습니다. 

Flutter의 위젯은 불변하기 때문에 가볍습니다.
위젯이 그 자체로 뷰가 아니기에 어떤 것도 직접 그리지 않고,
대신 UI에 대한 설명이며 내부적으로 이미 "inflate"된 실제 뷰 객체 UI의 의미론(semantics)이기 때문입니다.

Flutter는 [머티리얼 컴포넌트]({{site.material}}/develop/flutter/) 라이브러리를 포함합니다.
위젯은 [머티리얼 디자인 가이드라인]({{site.material}}/design/)을 따르고 있습니다.
머티리얼 디자인은 [모든 플랫폼에 최적화된]({{site.material}}/design/platform-guidance/cross-platform-adaptation.html#cross-platform-guidelines)
유연한 디자인 시스템입니다. 

그러나 Flutter는 모든 디자인 언어를 적용할 수 있을만큼 유연하고 표현력이 우수합니다.
예를 들어, iOS에서는 [쿠퍼티노(Cupertino) 위젯](/docs/development/ui/widgets/cupertino)을 적용하여
[애플의 iOS 디자인 언어](https://developer.apple.com/design/resources/)와 유사한 인터페이스를 만들 수 있습니다.

### `Widget`을 변경하는 방법은 무엇입니까?

Android에서는 뷰를 직접 수정하여 변경사항을 적용합니다. 
하지만 Flutter에서 `위젯`은 불변이기 때문에 직접 변경할 수 없고, 대신 위젯의 state를 변경할 수 있습니다. 

이게 Stateful 위젯과 Stateless 위젯 개념이 탄생한 배경입니다.
`StatelessWidget`은 말그대로 상태 정보가 없는 위젯입니다.  

`StatelessWidget`은 작성하려는 사용자 인터페이스 부분이 
구성 정보 외에 다른 어떤 정보에도 의존하지 않을 때 유용합니다.

예를 들어, Android에서 `ImageView`로 로고를 배치하는 경우와 비슷합니다.
로고는 실행 중에 변경되지 않기 때문에, Flutter에선 `StatelessWidget`를 사용합니다.

HTTP 호출이나 사용자와의 상호작용을 통해 받은 데이터를 기반으로 UI를 동적으로 변경하기를 원한다면
`StatefulWidget`을 사용할 수 있고 
Flutter 프레임워크에 `State`가 변경되었다고 알려주면 위젯이 변경됩니다.

중요한 점은 stateless와 stateful 모두
핵심 부분은 동일하게 동작한다는 점입니다.
둘 다 모든 프레임을 다시 빌드합니다,
차이는 `StatefulWidget`는 
프레임 전체에 걸쳐 상태를 데이터를 저장하고 다시반환하는 `State` 객체를 가지고 있다는 점입니다.

의문점이 남아있다면, 이 규칙을 항상 기억하세요: 
위젯이 변경되면 (예를 들어 사용자와 상호 작용으로 인해) stateful입니다.
하지만, 위젯이 변경되어도 상위 위젯 자신이 변경되지 않는다면, 
상위 위젯은 stateless 일 수 있습니다.

아래 예제는 `StatelessWidget`을 사용하는 방법을 보여줍니다. 
`Text` 위젯은 일반적인 `StatelessWidget`입니다.
`Text` 위젯의 구현을 보면 `StatelessWidget`의 하위클래스라는 걸 알 수 있습니다.

{% prettify dart %}
Text(
  'I like Flutter!',
  style: TextStyle(fontWeight: FontWeight.bold),
);
{% endprettify %}

보다시피, `Text` 위젯은 생성자로 전달된 것들을 그릴 뿐이고, 그 자체에 연결된 상태 정보는 없습니다.

하지만, 예를 들어 `FloatingActionButton`을 클릭할 때 
"I Like Flutter"를 동적으로 변경하고 싶다면 어떻게 할까요?

그러고 싶다면, `Text`를 `StatefulWidget`으로 감싼 뒤 
사용자가 버튼을 클릭할 때 그것을 변경하면 됩니다.

예시:

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  // Default placeholder text
  String textToShow = "I Like Flutter";

  void _updateText() {
    setState(() {
      // update the text
      textToShow = "Flutter is Awesome!";
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: Center(child: Text(textToShow)),
      floatingActionButton: FloatingActionButton(
        onPressed: _updateText,
        tooltip: 'Update Text',
        child: Icon(Icons.update),
      ),
    );
  }
}
{% endprettify %}

### 위젯을 어떻게 배치하나요? XML 레이아웃 파일은 어디에 있나요?

Android에서는 레이아웃을 XML에 작성하지만, Flutter에서는 레이아웃을 위젯 트리로 작성합니다. 

아래 예시는 여백과 함께 간단한 위젯을 배치하는 방법을 보여줍니다:

{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: Center(
        child: MaterialButton(
          onPressed: () {},
          child: Text('Hello'),
          padding: EdgeInsets.only(left: 10.0, right: 10.0),
        ),
      ),
    );
  }
{% endprettify %}


[위젯 카탈로그](/docs/development/ui/widgets/layout)에서
Flutter가 제공하는 레이아웃을 볼 수 있습니다.

### 어떻게 하면 레이아웃에서 컴포넌트를 추가하거나 제거할 수 있나요?

Android에서는 `addChild()` 혹은 `removeChild()`를 호출하여 
동적으로 자식 뷰를 추가하거나 제거합니다.
Flutter에서는 위젯이 불변이기 때문에 `addChild()`와 동일한 명령은 없습니다.
대신, 부모에게 위젯을 리턴하는 함수를 전달하고, 
그 자식의 생성 여부를 boolean flag로 제어할 수 있습니다.  

예시로, `FloatingActionButton`을 클릭했을 때 두 위젯을 번갈아 보여주는 방법입니다: 

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  // Default value for toggle
  bool toggle = true;
  void _toggle() {
    setState(() {
      toggle = !toggle;
    });
  }

  _getToggleChild() {
    if (toggle) {
      return Text('Toggle One');
    } else {
      return MaterialButton(onPressed: () {}, child: Text('Toggle Two'));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: Center(
        child: _getToggleChild(),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _toggle,
        tooltip: 'Update Text',
        child: Icon(Icons.update),
      ),
    );
  }
}
{% endprettify %}

### 위젯에 애니메이션을 넣는 방법은?

Android에서는 XML을 사용하거나 뷰에서 `animate()` 메서드를 호출하여 애니메이션을 만듭니다.
Flutter에서는 애니메이션용 위젯으로 위젯을 감싸는 방식으로 애니메이션 라이브러리를 사용하여 애니메이션을 만드세요.

Flutter에서는 중지하고, 탐색하고, 정지하고, 되감기할 수 있는 `Animation<double>`인 `AnimationController`를 사용하세요.
vsync가 발생할 때 알려주고 
작동하는 동안 각 프레임에서 0과 1 사이의 직선 궤적(linear interpolation)을 생성하는 `Ticker`도 필요합니다. 
그런 다음 1개 이상의 `Animation`을 만들어 controller에 연결하세요. 

예를 들어, `CurvedAnimation`을 사용하여 곡선 궤적(interpolated curve)을 따르는 애니메이션을 구현할 수 있습니다.
이러한 의미에서 controller는 애니메이션 진행의 "주" 원천이고
`CurvedAnimation`은 계산하는 기본 선형 동작 대신 커브로 대체하여 계산합니다.
Flutter에서는 애니메이션도 위젯처럼 구성물입니다.

위젯 트리를 만들 때
`Animation`을  `FadeTransition`의 opacity와 같은 
애니메이션이 필요한 속성에 할당하고 
controller에 애니메이션을 시작하도록 지시합니다.

다음 예에서는 `FloatingActionButton`을 누르면
위젯이 로고로 바뀌는 `FadeTransition`을 작성하는 방법을 보여줍니다.

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(FadeAppTest());
}

class FadeAppTest extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Fade Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyFadeTest(title: 'Fade Demo'),
    );
  }
}

class MyFadeTest extends StatefulWidget {
  MyFadeTest({Key key, this.title}) : super(key: key);
  final String title;
  @override
  _MyFadeTest createState() => _MyFadeTest();
}

class _MyFadeTest extends State<MyFadeTest> with TickerProviderStateMixin {
  AnimationController controller;
  CurvedAnimation curve;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
    curve = CurvedAnimation(parent: controller, curve: Curves.easeIn);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
          child: Container(
              child: FadeTransition(
                  opacity: curve,
                  child: FlutterLogo(
                    size: 100.0,
                  )))),
      floatingActionButton: FloatingActionButton(
        tooltip: 'Fade',
        child: Icon(Icons.brush),
        onPressed: () {
          controller.forward();
        },
      ),
    );
  }
}
{% endprettify %}

자세한 내용은 
[애니메이션 & 동작 위젯](/docs/development/ui/widgets/animation), 
[애니메이션 튜토리얼](/docs/development/ui/animations/tutorial),
[애니메이션 개요](/docs/development/ui/animations)를
참조하세요

### `Canvas`를 사용하여 그리는 방법은?

화면에 이미지와 모양을 그리기 위해 Android에서는 `Canvas`와 `Drawable`을 사용합니다.
Flutter도 저수준(low-level) 렌더링 엔진인 Skia를 사용하기 때문에 `Canvas`와 유사한 API를 가지고 있습니다,
그렇기 때문에 Flutter에서 캔버스에 그리는 것은 Android 개발자에게 매우 익숙한 작업입니다. 

Flutter는 캔버스에 그리는 일을 도와주는 2개의 클래스를 가지고 있습니다.
`CustomPaint`와 `CustomPainter`입니다. 
`CustomPainter`는 캔버스에 어떻게 그릴지 알고리즘을 구현합니다.

Flutter에 서명 그림 그리기를 구현하는 방법은 [StackOverflow][]에 있는 Collin의 답변을 참고하세요.

[StackOverflow]: {{site.so}}/questions/46241071/create-signature-area-for-mobile-app-in-dart-flutter

{% prettify dart %}
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(home: DemoApp()));

class DemoApp extends StatelessWidget {
  Widget build(BuildContext context) => Scaffold(body: Signature());
}

class Signature extends StatefulWidget {
  SignatureState createState() => SignatureState();
}

class SignatureState extends State<Signature> {
  List<Offset> _points = <Offset>[];
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanUpdate: (DragUpdateDetails details) {
        setState(() {
          RenderBox referenceBox = context.findRenderObject();
          Offset localPosition =
          referenceBox.globalToLocal(details.globalPosition);
          _points = List.from(_points)..add(localPosition);
        });
      },
      onPanEnd: (DragEndDetails details) => _points.add(null),
      child: CustomPaint(painter: SignaturePainter(_points), size: Size.infinite),
    );
  }
}

class SignaturePainter extends CustomPainter {
  SignaturePainter(this.points);
  final List<Offset> points;
  void paint(Canvas canvas, Size size) {
    var paint = Paint()
      ..color = Colors.black
      ..strokeCap = StrokeCap.round
      ..strokeWidth = 5.0;
    for (int i = 0; i < points.length - 1; i++) {
      if (points[i] != null && points[i + 1] != null)
        canvas.drawLine(points[i], points[i + 1], paint);
    }
  }
  bool shouldRepaint(SignaturePainter other) => other.points != points;
}
{% endprettify %}

### 커스텀 위젯을 만드는 방법은?

Android에서는 
보통 `View`의 하위클래스를 만들거나 이미 있는 View를 사용하여
원하는 동작을 수행하는 메서드를 오버라이드하고 구현합니다.

Flutter에서는 
[더 작은 위젯들로 구성된](/docs/resources/technical-overview#everythings-a-widget) 새로운 위젯을 만듭니다
(상속 대신).

이것은 구성물(building block)이 이미 준비되어 있는 상황에서 약간 다른 동작을 만들고 싶을 때
(예를 들면, 새로운 레이아웃 로직을 첨가하고 싶을 때)
Android에서 `ViewGroup`을 만드는 것과 유사합니다.

예를 들어, `CustomButton`의 생성자가 label을 받게 하려면 어떻게 해야 할까요?
`RaisedButton`을 상속하는 대신,
`RaisedButton`을 감싸고 있는 CustomButton을 만들고 생성자에 label을 받게 하면 됩니다.

{% prettify dart %}
class CustomButton extends StatelessWidget {
  final String label;

  CustomButton(this.label);

  @override
  Widget build(BuildContext context) {
    return RaisedButton(onPressed: () {}, child: Text(label));
  }
}
{% endprettify %}

그런 다음 다른 Flutter 위젯처럼 `CustomButton`을 사용하기만 하면 됩니다:

{% prettify dart %}
@override
Widget build(BuildContext context) {
  return Center(
    child: CustomButton("Hello"),
  );
}
{% endprettify %}

## Intents (인텐트)

### Flutter에서 `Intent`와 동일한 것은?

Android에서 `Intent`는 두 가지 용도로 사용됩니다:
엑티비티 간 이동, 그리고 다른 컴포넌트와 통신할 때입니다.
반면, Flutter는 intent라는 개념을 가지고 있지 않습니다.
네이티브 통합을 활용하여 intent를 사용할 수는 있습니다.
([플러그인]({{site.pub}}/packages/android_intent)을 사용)

Flutter는 실제로 액티비티나 프래그먼트와 직접적으로 동등한 요소를 가지고 있지 않습니다.
Flutter에서는 하나의 `Activity`안에서 `Navigator`와 `Route`를 활용하여 스크린 간 내비게이션을 합니다.

`Route`는 앱의 “스크린”이나 “페이지”를 추상화한 것이고,
`Navigator`는 route를 관리하는 위젯입니다.
`Activity`와 route는 유사하지만, 똑같은 의미는 아닙니다.
Navigator는 routes를 push나 pop을 하여 스크린 간 이동을 할 수 있습니다.
Navigator는 스택처럼 동작합니다. 
이동하려는 새로운 route로 `push()`할 수 있고,
되돌아가려면 `pop()`하면 됩니다.

안드로이드에서는 앱의 `AndroidManifest.xml`에 액티비티를 정의합니다.

Flutter에서는 페이지 간 이동을 하는 몇 가지 방법이 있습니다:

* route 이름의 `Map`을 지정한다. (MaterialApp)
* route로 바로 이동한다. (WidgetApp)

아래는 Map을 지정하는 방식입니다.

{% prettify dart %}
 void main() {
  runApp(MaterialApp(
    home: MyAppHome(), // becomes the route named '/'
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => MyPage(title: 'page A'),
      '/b': (BuildContext context) => MyPage(title: 'page B'),
      '/c': (BuildContext context) => MyPage(title: 'page C'),
    },
  ));
}
{% endprettify %}

`Navigator`에 route의 이름을 `push`하여 이동할 수 있습니다. 

{% prettify dart %}
Navigator.of(context).pushNamed('/b');
{% endprettify %}

`Intent`의 또다른 용도는 카메라나 파일 선택 도구같은 외부 컴포넌트를 를 호출하는 것입니다.
이 작업을 위해서는 네이티브 플랫폼 통합이 필요합니다.
(또는 [이미 있는 플러그인]({{site.pub}}/flutter/)을 활용하는 방법도 있습니다)

네이티브 플랫폼 통합을 배우기 위해서는, 
[패키지 및 플러그인 개발](/docs/development/packages-and-plugins/developing-packages)를 
참조하세요.

### 앱 외부에서 intent가 넘어올 때는 어떻게 처리해야?  

Flutter는 안드로이드 레이어와 직접 통신하고
공유된 데이터를 요청하여 외부에서 넘어오는 intent를 처리할 수 있습니다.   

아래 예제는
Flutter 코드를 실행하는 네이티브 액티비티에서 텍스트를 공유하는 intent filter를 등록해서,
다른 앱은 Flutter 앱과 텍스트를 공유할 수 있도록 합니다.

기본 흐름은 
먼저 안드로이드 네이티브 쪽(우리의 `Activity`)에 공유된 텍스트 데이터를 처리하고,
Flutter가 `MethodChannel`을 이용하여 데이터를 요청할 때까지 기다립니다.

먼저, `AndroidManifest.xml`에 모든 intent를 위한 intent filter를 등록하세요:

{% prettify xml %}
<activity
  android:name=".MainActivity"
  android:launchMode="singleTop"
  android:theme="@style/LaunchTheme"
  android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection"
  android:hardwareAccelerated="true"
  android:windowSoftInputMode="adjustResize">
  <!-- ... -->
  <intent-filter>
    <action android:name="android.intent.action.SEND" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="text/plain" />
  </intent-filter>
</activity>
{% endprettify %}

그런 다음 `MainActivity`에서 intent를 처리하고, intent에 공유된 텍스트를 추출한 후 저장해둡니다.
처리를 시작할 준비가 되면 Flutter가 플랫폼 채널을 사용해서 데이터를 요청하고, 네이티브 쪽에서 데이터가 전송됩니다.

{% prettify java %}
package com.example.shared;

import android.content.Intent;
import android.os.Bundle;

import java.nio.ByteBuffer;

import io.flutter.app.FlutterActivity;
import io.flutter.plugin.common.ActivityLifecycleListener;
import io.flutter.plugin.common.MethodCall;
import io.flutter.plugin.common.MethodChannel;
import io.flutter.plugin.common.MethodChannel.MethodCallHandler;
import io.flutter.plugins.GeneratedPluginRegistrant;

public class MainActivity extends FlutterActivity {

  private String sharedText;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    GeneratedPluginRegistrant.registerWith(this);
    Intent intent = getIntent();
    String action = intent.getAction();
    String type = intent.getType();

    if (Intent.ACTION_SEND.equals(action) && type != null) {
      if ("text/plain".equals(type)) {
        handleSendText(intent); // Handle text being sent
      }
    }

    new MethodChannel(getFlutterView(), "app.channel.shared.data").setMethodCallHandler(
      new MethodCallHandler() {
        @Override
        public void onMethodCall(MethodCall call, MethodChannel.Result result) {
          if (call.method.contentEquals("getSharedText")) {
            result.success(sharedText);
            sharedText = null;
          }
        }
      });
  }

  void handleSendText(Intent intent) {
    sharedText = intent.getStringExtra(Intent.EXTRA_TEXT);
  }
}
{% endprettify %}

마지막으로, 위젯이 렌더링 될 때 Flutter쪽에서 데이터를 요청하도록 하세요:

{% prettify dart %}
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample Shared App Handler',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  static const platform = const MethodChannel('app.channel.shared.data');
  String dataShared = "No data";

  @override
  void initState() {
    super.initState();
    getSharedText();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(body: Center(child: Text(dataShared)));
  }

  getSharedText() async {
    var sharedData = await platform.invokeMethod("getSharedText");
    if (sharedData != null) {
      setState(() {
        dataShared = sharedData;
      });
    }
  }
}
{% endprettify %}

### `startActivityForResult()`와 동일한 것은?


`Navigator` 클래스는 Flutter에서 route를 처리해주고, 스택에 푸시한 route에서 결과를 다시 얻을 때 사용됩니다.
`push()`에서 리턴되는 `Future`를 `await`해서 이 작업을 할 수 있습니다.

예를 들어, 아래 코드처럼 사용자가 위치를 선택할 수 있도록 location route로 이동할 수 있습니다:

{% prettify dart %}
Map coordinates = await Navigator.of(context).pushNamed('/location');
{% endprettify %}

그런 다음, location route 안에서, 사용자가 위치를 선택하면 결과와 함께 스택에서 `pop`을 할 수 있습니다: 

{% prettify dart %}
Navigator.of(context).pop({"lat":43.821757,"long":-79.226392});
{% endprettify %}

## 비동기 UI

### Flutter에서 `runOnUiThread()`와 동일한 것은?

Dart는 단일-스레드 실행 모델을 가지고 있고,
`Isolate`(Dart 코드를 다른 스레드에서 실행하는 방법)와
이벤트 루프, 비동기 프로그래밍을 지원합니다.
`Isolate`를 생성하지 않는 한, Dart 코드는 메인 UI 스레드에서 실행되고 이벤트 루프에 의해 구동됩니다.
Flutter의 이벤트 루프는 안드로이드의 메인 `Looper`, 즉  메인 스레드에 연결되어 있는 `Looper`와 동일합니다.

Dart의 단일-스레드 모델은 모든 걸 동기로 실행하여 UI 끊김을 유발하지는 않습니다.
항상 메인 스레드를 차단하지 말아야 하는 안드로이드와 달리,
Flutter에서는 `async`/`await`와 같은 
Dart 언어가 제공하는 비동기 기능을 사용하여 
비동기 작업을 수행할 수 있습니다.
C#이나 Javascript, 또는 코틀린의 coroutine을 사용해봤다면
`async`/`await` 패러다임에 이미 익숙할 것입니다.

예를 들어, 복잡한 부분은 Dart에게 맡겨두고, 
`async`/`await`를 활용하여 UI 끊김 없이 네트워크 코드를 실행할 수 있습니다:

{% prettify dart %}
loadData() async {
  String dataURL = "https://jsonplaceholder.typicode.com/posts";
  http.Response response = await http.get(dataURL);
  setState(() {
    widgets = json.decode(response.body);
  });
}
{% endprettify %}

`await`했던 네트워크 요청이 완료되면, 
위젯 서브트리를 다시 빌드하고 데이터를 업데이트하도록 
`setState()`로 UI를 업데이트하세요.

아래는 데이터를 비동기로 받아온 후 `ListView`에 보여주는 예제입니다:   

{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();

    loadData();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: ListView.builder(
          itemCount: widgets.length,
          itemBuilder: (BuildContext context, int position) {
            return getRow(position);
          }));
  }

  Widget getRow(int i) {
    return Padding(
      padding: EdgeInsets.all(10.0),
      child: Text("Row ${widgets[i]["title"]}")
    );
  }

  loadData() async {
    String dataURL = "https://jsonplaceholder.typicode.com/posts";
    http.Response response = await http.get(dataURL);
    setState(() {
      widgets = json.decode(response.body);
    });
  }
}
{% endprettify %}

백그라운드에서 작업하는 방법, 그리고 Flutter가 안드로이드와 어떻게 다른지
더 알고 싶으시면 다음 섹션을 참조하세요.

### 백그라운드 스레드로 이동하여 작업하는 방법은? 

안드로이드에서는 
메인 스레드를 차단하지 않아야 하고 애플리케이션 응답 없음(ANR)을 피해야 하기 때문에 
보통 네트워크 리소스에 접근하기 위해서 백그라운드 스레드로 이동하여 작업해야 합니다.
예를 들면, `AsyncTask`, `LiveData`, `IntentService`, `JobScheduler` 작업, 
또는 백그라운드 스레드에서 작동하는 스케줄러에서 RxJava 파이프라인을 사용해야 할 것입니다.

Flutter는 (Node.js 처럼) 단일 스레드에서 동작하고 이벤트 루프를 실행하므로, 
스레드 관리나 백그라운드 스레드 생성을 걱정할 필요가 없습니다.
저장 장치 접근이나 네트워크 요청 같은 I/O 위주의 작업을 수행하려면,
`async`/`await`를 사용해 안전하게 처리할 수 있고, 그렇게만 하면 모든 준비는 끝입니다.
반대로, CPU를 계속 많이 사용하는 계산 집약적인 작업을 해야 한다면,
안드로이드의 메인 스레드에서 _어떤_ 종류의 작업도 하지 않는 것처럼
이벤트 루프가 차단되는 것을 막기 위해 `독립적인(Isolate)` 곳에서 작업을 수행하고 싶을 겁니다.

I/O 위주의 작업의 경우, 함수를 `async` 함수로 선언하고,
함수 안에서 오래 걸리는 작업을 `await` 하세요:

{% prettify dart %}
loadData() async {
  String dataURL = "https://jsonplaceholder.typicode.com/posts";
  http.Response response = await http.get(dataURL);
  setState(() {
    widgets = json.decode(response.body);
  });
}
{% endprettify %}

이것은 I/O 작업인 네트워크 요청이나 데이터베이스 요청을 할 때 일반적으로 사용하는 방법입니다.

안드로이드에서는 `AsyncTask`를 상속받으면, 
보통 `onPreExecute()`, `doInBackground()`, `onPostExecute()` 3개의 메서드를 오버라이드 해야 합니다.
Flutter에는 이런 메서드가 없습니다.
수행 시간이 긴 함수를 `await`하면 Dart의 이벤트 루프가 나머지를 처리해주기 때문입니다. 

그러나, 너무 많은 데이터를 처리할 때는 UI에 정체 현상이 일어날 수 있습니다.
Flutter에서는 긴 작업이나 계산 집약적인 작업을 할 때
여러 개의 CPU 코어를 활용하기 위해 `Isolate`를 사용합니다.

Isolate는 메인 메모리 힙과 메모리를 전혀 공유하지 않는 별도의 실행 스레드입니다.
`setState()`를 호출하여 UI를 업데이트할 수 없고, 메인 스레드에서 변수에 접근할 수 없다는 뜻입니다.
안드로이드의 스레드와 다르게, Isolate는 이름에서 파악할 수 있듯이 메모리를 공유할 수 없습니다 (예를 들면, 정적 필드 형태로).

아래 간단한 Isolate 예시는
메인 스레드로 데이터를 다시 공유하여 UI를 업데이트하는 방법을 보여줍니다. 

{% prettify dart %}
loadData() async {
  ReceivePort receivePort = ReceivePort();
  await Isolate.spawn(dataLoader, receivePort.sendPort);

  // The 'echo' isolate sends its SendPort as the first message
  SendPort sendPort = await receivePort.first;

  List msg = await sendReceive(sendPort, "https://jsonplaceholder.typicode.com/posts");

  setState(() {
    widgets = msg;
  });
}

// The entry point for the isolate
static dataLoader(SendPort sendPort) async {
  // Open the ReceivePort for incoming messages.
  ReceivePort port = ReceivePort();

  // Notify any other isolates what port this isolate listens to.
  sendPort.send(port.sendPort);

  await for (var msg in port) {
    String data = msg[0];
    SendPort replyTo = msg[1];

    String dataURL = data;
    http.Response response = await http.get(dataURL);
    // Lots of JSON to parse
    replyTo.send(json.decode(response.body));
  }
}

Future sendReceive(SendPort port, msg) {
  ReceivePort response = ReceivePort();
  port.send([msg, response.sendPort]);
  return response.first;
}
{% endprettify %}

여기서, `dataLoader()`는 별도의 실행 스레드에서 실행되는 `Isolate`입니다.
Isolate에서 더 CPU 집약적인 작업(예를 들면, 아주 큰 JSON을 파싱하는), 
또는 암호화나 신호 처리 같은 계산 집약적인 작업을 수행할 수 있습니다. 

아래 실행할 수 있는 전체 예제가 있습니다:

{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:async';
import 'dart:isolate';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
    loadData();
  }

  showLoadingDialog() {
    if (widgets.length == 0) {
      return true;
    }

    return false;
  }

  getBody() {
    if (showLoadingDialog()) {
      return getProgressDialog();
    } else {
      return getListView();
    }
  }

  getProgressDialog() {
    return Center(child: CircularProgressIndicator());
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text("Sample App"),
        ),
        body: getBody());
  }

  ListView getListView() => ListView.builder(
      itemCount: widgets.length,
      itemBuilder: (BuildContext context, int position) {
        return getRow(position);
      });

  Widget getRow(int i) {
    return Padding(padding: EdgeInsets.all(10.0), child: Text("Row ${widgets[i]["title"]}"));
  }

  loadData() async {
    ReceivePort receivePort = ReceivePort();
    await Isolate.spawn(dataLoader, receivePort.sendPort);

    // The 'echo' isolate sends its SendPort as the first message
    SendPort sendPort = await receivePort.first;

    List msg = await sendReceive(sendPort, "https://jsonplaceholder.typicode.com/posts");

    setState(() {
      widgets = msg;
    });
  }

  // the entry point for the isolate
  static dataLoader(SendPort sendPort) async {
    // Open the ReceivePort for incoming messages.
    ReceivePort port = ReceivePort();

    // Notify any other isolates what port this isolate listens to.
    sendPort.send(port.sendPort);

    await for (var msg in port) {
      String data = msg[0];
      SendPort replyTo = msg[1];

      String dataURL = data;
      http.Response response = await http.get(dataURL);
      // Lots of JSON to parse
      replyTo.send(json.decode(response.body));
    }
  }

  Future sendReceive(SendPort port, msg) {
    ReceivePort response = ReceivePort();
    port.send([msg, response.sendPort]);
    return response.first;
  }
}
{% endprettify %}

### Flutter에서 OkHttp와 동일한 것은?

널리 사용되는 [`http` 패키지](https://pub.dartlang.org/packages/http)를 사용하면 
Flutter에서 네트워크 요청을 쉽게 할 수 있습니다.

OkHttp의 모든 기능이 http 패키지에 있지는 않지만,
일반적으로 구현할 수 있는 네트워킹의 많은 부분이 추상화되어 있어, 네트워크 호출을 쉽게 수행할 수 있습니다.

`http` 패키지를 사용하기 위해, `pubspec.yaml`에 의존성를 추가하세요:

{% prettify yaml %}
dependencies:
  ...
  http: ^0.11.3+16
{% endprettify %}

네트워크를 호출하기 위해 `async` 함수인 `http.get()`을 `await` 하세요:

{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
[...]
  loadData() async {
    String dataURL = "https://jsonplaceholder.typicode.com/posts";
    http.Response response = await http.get(dataURL);
    setState(() {
      widgets = json.decode(response.body);
    });
  }
}
{% endprettify %}

### 시간이 오래 걸리는 작업을 할 때 어떻게 진행 상태를 표시할 수 있을까요?

안드로이드에서는 보통
백그라운드 스레드에서 긴 작업을 수행하는 동안
UI에 `ProgressBar` 뷰를 표시합니다.

Flutter에서는 `ProgressIndicator` 위젯을 사용합니다.
렌더링 되는 시점을 boolean으로 제어하여 진행 상태를 표시하세요.
긴 작업이 시작되기 전에 Flutter에게 위젯의 상태를 변경해야 한다고 알려주고, 
작업이 끝나면 위젯을 숨기세요. 

아래 예시에서는 빌드 함수를 3개로 분리합니다.
`showLoadingDialog()`가 `true` 이면 (`widgets.length == 0`일 때),
`ProgressIndicator`를 그립니다.
그렇지 않은 상황에서는 네트워크 요청을 통해 얻은 데이터를 활용하여 `ListView`를 그립니다.

{% prettify dart %}
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = [];

  @override
  void initState() {
    super.initState();
    loadData();
  }

  showLoadingDialog() {
    return widgets.length == 0;
  }

  getBody() {
    if (showLoadingDialog()) {
      return getProgressDialog();
    } else {
      return getListView();
    }
  }

  getProgressDialog() {
    return Center(child: CircularProgressIndicator());
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text("Sample App"),
        ),
        body: getBody());
  }

  ListView getListView() => ListView.builder(
      itemCount: widgets.length,
      itemBuilder: (BuildContext context, int position) {
        return getRow(position);
      });

  Widget getRow(int i) {
    return Padding(padding: EdgeInsets.all(10.0), child: Text("Row ${widgets[i]["title"]}"));
  }

  loadData() async {
    String dataURL = "https://jsonplaceholder.typicode.com/posts";
    http.Response response = await http.get(dataURL);
    setState(() {
      widgets = json.decode(response.body);
    });
  }
}
{% endprettify %}

## 프로젝트 구조 & 자원

### 해상도별 이미지 파일은 어디에 저장하나요?

안드로이드는 리소스와 asset을 별개의 항목으로 다루지만, Flutter 앱은 asset만 가지고 있습니다.
안드로이드의 `res/drawable-*` 폴더에 있는 모든 리소스는 Flutter의 assets 폴더에 저장됩니다. 

Flutter는 iOS처럼 단순한 해상도 기반 형식을 사용합니다. 
`1.0x`, `2.0x`, `3.0x`, 혹은 다른 배율의 asset이 있을 수 있습니다.
Flutter는 `dp`를 사용하지 않지만, 기본적으로 장비 독립적인 픽셀과 동일한 논리적 픽셀을 사용합니다.
이른바
[`devicePixelRatio`]({{site.api}}/flutter/dart-ui/Window/devicePixelRatio.html)
는 하나의 논리적 픽셀에서 물리적 픽셀의 비율을 나타냅니다. 

안드로이드의 해상도 단위와 비교하면 아래와 같습니다:

 안드로이드 해상도 단위 | Flutter 픽셀 비율
 --- | ---
 `ldpi` | `0.75x`
 `mdpi` | `1.0x`
 `hdpi` | `1.5x`
 `xhdpi` | `2.0x`
 `xxhdpi` | `3.0x`
 `xxxhdpi` | `4.0x`


어느 곳에나 asset 폴더를 만들 수 있습니다. Flutter는 미리 정의된 폴더 구조가 없습니다.
`pubspec.yaml` 파일에 asset을 (위치와 함께) 선언하면, Flutter가 추출해갑니다.

Flutter 1.0 베타 2 이전에는 Flutter 안에 정의된 asset을 네이티브 쪽에서 접근할 수 없었으며, 
그 반대의 경우에도 분리된 폴더에 있기 때문에 네이티브 asset과 리소스에 Flutter가 접근할 수 없었습니다.

Flutter 베타 2부터는 asset이 네이티브 asset 폴더에 저장되고, 
안드로이드의 `AssetManager`를 사용하여 네이티브 쪽에서 asset에 접근할 수 있습니다:

{% prettify kotlin %}
val flutterAssetStream = assetManager.open("flutter_assets/assets/my_flutter_asset.png")
{% endprettify %}

Flutter 베타 2에서도 Flutter가 네이티브 리소스와 네이티브 asset에 접근할 수는 없습니다. 

예를 들어, `my_icon.png`라는 새로운 이미지 asset을 새로운 Flutter 프로젝트에 추가하기 위해,
임의로 `images`라는 폴더에 담아야 한다고 정하면,
기본 이미지 (1.0x)를 `images` 폴더에 넣고,
적합한 배율을 폴더 이름으로 지정하여 하위 폴더에 다른 변형 이미지들을 넣을 수 있습니다:

```
images/my_icon.png       // Base: 1.0x image
images/2.0x/my_icon.png  // 2.0x image
images/3.0x/my_icon.png  // 3.0x image
```

다음으로 `pubspec.yaml`에 이 이미지들을 선언해야 합니다:

{% prettify yaml %}
assets:
 - images/my_icon.jpeg
{% endprettify %}

그러면 이제 `AssetImage`를 사용하여 이미지에 접근할 수 있습니다:

{% prettify dart %}
return AssetImage("images/a_dot_burr.jpeg");
{% endprettify %}

혹은 바로 `Image` 위젯을 사용할 수도 있습니다:

{% prettify dart %}
@override
Widget build(BuildContext context) {
  return Image.asset("images/my_image.png");
}
{% endprettify %}

### 어디에 문자열을 저장하나요? 현지화는 어떻게 처리하나요?

현재 Flutter에는 문자열 전용 리소스 같은 시스템이 없습니다.
현재로서는 클래스의 텍스트를 정적 필드로 담아둔 후 거기에 접근하는 게 가장 좋습니다.
예를 들면:

{% prettify dart %}
class Strings {
  static String welcomeMessage = "Welcome To Flutter";
}
{% endprettify %}

그런 다음 코드에서 문자열에 아래와 같이 액세스할 수 있습니다:

{% prettify dart %}
Text(Strings.welcomeMessage)
{% endprettify %}

Flutter는 안드로이드의 접근성에 대한 기본적인 지원을 제공하지만, 이 기능은 아직 개발 진행 중입니다.

국제화 및 현지화를 위해 [intl 패키지]({{site.pub}}/packages/intl)를 사용하시기를 권장합니다.

### Gradle 파일과 동일한 것은? 의존성을 어떻게 추가하나요?

안드로이드에서는 Gradle 빌드 스크립트를 추가하여 의존성을 추가합니다.
Flutter는 Dart의 자체 빌드 시스템과 Pub 패키지 관리자를 사용합니다.
이 도구들은 네이티브 안드로이드와 iOS 래퍼 앱의 빌드를 각각의 빌드 시스템에 위임합니다.

Flutter 프로젝트 안에 있는 `android` 폴더 아래에 Gradle 파일이 있지만,
각 플래폼별 네이티브 의존성을 추가할 때에만 이 파일을 사용하세요.
일반적인 경우에는, `pubspec.yaml` 파일을 사용하여 Flutter에서 사용하는 외부 의존성을 추가하세요.
Flutter 패키지를 찾기 좋은 곳은 [Pub]({{site.pub}}/flutter/packages/)입니다.
 
## 액티비티와 프래그먼트 

### Flutter에서 액티비티, 프래그먼트와 동일한 것은?

안드로이드에서, `액티비티`는 사용자가 할 수 있는 것을 모아 나타냅니다.
`프래그먼트`는 사용자 인터페이스의 일부 또는 동작을 나타냅니다. 
프래그먼트는 코드를 모듈화하고,
더 큰 화면을 위한 정교한 사용자 인터페이스를 구성하며,
앱 UI을 확장하는 데 도움을 줍니다.
Flutter에서는 이 두 개념 모두 `위젯`의 범주에 포함됩니다.

액티비티와 프래그먼트를 만들기 위한 UI에 대해 더 자세히 알고 싶다면,
커뮤니티 공헌자의 글 
[안드로이드 개발자를 위한 Flutter: Flutter에서 액티비티 UI를 디자인하는 방법]({{site.medium}}/@burhanrashid52/flutter-for-android-developers-how-to-design-activity-ui-in-flutter-4bf7b0de1e48)을
참조하세요.


[인텐트](#what-is-the-equivalent-of-an-intent-in-flutter) 부분에서 언급한 것처럼, 
Flutter에서 모든 것은 위젯이기 때문에, 
Flutter에서 화면은 `위젯`들로 표현됩니다. 
다른 화면이나 페이지인 `Route` 간 이동을 위해 `Navigator`를 사용할 수도 있고,
혹은 그냥 상태만 바꾸거나, 같은 데이터를 랜더링할 수 있습니다.

### 안드로이드 액티비티의 생명주기 이벤트를 어떻게 수신할 수 있나요?

안드로이드에서는 `액티비티`에 있는 메서드를 오버라이드하여, 액티비티 자체에 있는 생명주기를 메서드를 수정하거나
`Application`에 `ActivityLifecycleCallbacks`를 등록할 수 있습니다.
Flutter에서는 위와 같은 개념은 없지만, 
대신 `WidgetsBinding` 옵저버에 연결하고 
`didChangeAppLifecycleState()` 변경 이벤트를 수신하여
생명주기 이벤트를 수신할 수 있습니다.

관찰 가능한 생명주기 이벤트는 다음과 같습니다:

* `inactive` — 앱이 비활성화 상태이고 사용자의 입력을 받지 않습니다.
  안드로이드에서 동일한 이벤트가 없기 때문에 이 이벤트는 iOS에서만 동작합니다.   
* `paused` — 앱이 현재 사용자에게 보이지 않고, 사용자의 입력을 받지 않으며, 백그라운드에서 동작 중입니다.
  안드로이드의 `onPause()`와 동일합니다.
* `resumed` — 앱이 보이고 있고 사용자 입력을 받고 있습니다.
  안드로이드의 `onPostResume()`와 동일합니다.
* `suspending` — 앱이 일시 중지 되었습니다. 안드로이드에서 `onStop`과 동일합니다.
  iOS에서는 동일한 이벤트가 없기 때문에 호출되지 않습니다.

이 상태들의 의미에 대해 자세한 정보를 알고 싶으시다면, 
[`AppLifecycleStatus` 문서][]를 참조하세요.

[`AppLifecycleStatus` 문서]: {{site.api}}/flutter/dart-ui/AppLifecycleState-class.html

눈치채셨겠지만, 아주 소수의 액티비티 생명주기만 이용가능합니다;
`FlutterActivity`가 내부적으로 거의 모든 액티비티 생명주기를 캡처하여 Flutter 앤진으로 보내기는 하지만,
대부분은 보호되어 있습니다.
Flutter가 엔진을 시작하고 중지하는 일을 처리하고, 
대부분의 경우 Flutter 측의 액티비티 생명주기를 관찰할 이유는 거의 없습니다.
네이티브 리소스를 얻거나 배포하기 위해 생명주기를 관찰할 필요가 있다면,
어찌됐든 네이티브 쪽에서 수행해야 할 것입니다.

아래에 포함된 액티비티의 생명주기 상태를 관찰하는 방법의 예시가 있습니다:

{% prettify dart %}
import 'package:flutter/widgets.dart';

class LifecycleWatcher extends StatefulWidget {
  @override
  _LifecycleWatcherState createState() => _LifecycleWatcherState();
}

class _LifecycleWatcherState extends State<LifecycleWatcher> with WidgetsBindingObserver {
  AppLifecycleState _lastLifecycleState;

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    setState(() {
      _lastLifecycleState = state;
    });
  }

  @override
  Widget build(BuildContext context) {
    if (_lastLifecycleState == null)
      return Text('This widget has not observed any lifecycle changes.', textDirection: TextDirection.ltr);

    return Text('The most recent lifecycle state this widget observed was: $_lastLifecycleState.',
        textDirection: TextDirection.ltr);
  }
}

void main() {
  runApp(Center(child: LifecycleWatcher()));
}
{% endprettify %}

## 레이아웃

### LinearLayout과 동일한 것은?

안드로이드에서는 LinearLayout을 사용하여 수평 또는 수직의 선형으로 위젯을 배치합니다.
Flutter에서는 Row 위젯과 Column 위젯을 사용하여 동일한 결과를 얻을 수 있습니다.

두 코드 샘플이 "Row" 및 "Column" 위젯을 제외하고 같은 형태라는 것을 알 수 있습니다.
children은 똑같고, 이 기능은 children은 같으면서도 계속 변하는 풍부한 레이아웃을 구현하기 위해 이용될 수 있습니다.

{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        Text('Row One'),
        Text('Row Two'),
        Text('Row Three'),
        Text('Row Four'),
      ],
    );
  }
{% endprettify %}

{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        Text('Column One'),
        Text('Column Two'),
        Text('Column Three'),
        Text('Column Four'),
      ],
    );
  }
{% endprettify %}

linear layouts에 대해 더 알고 싶다면, 커뮤니티 공헌자의 미디엄 글 
[안드로이드 개발자를 위한 Flutter : Flutter에서 LinearLayout을 어떻게 디자인 하는가?]({{site.medium}}/@burhanrashid52/flutter-for-android-developers-how-to-design-linearlayout-in-flutter-5d819c0ddf1a)
를 참조하세요.

### RelativeLayout과 동일한 것은?

RelativeLayout은 위젯을 서로 상대적이게 배치합니다. 
Flutter에도 동일한 결과를 얻을 수 있는 방법이 몇 가지 있습니다.

Column, Row, Stack 위젯을 조합하여 RelativeLayout와 동일한 결과를 얻을 수 있습니다.
위젯 생성자에서 부모를 기준으로 자식을 어떻게 배치할지를 지정할 수 있습니다.

Flutter에서 RelativeLayout을 작성하는 좋은 예시를 찾고 있다면, 
[StackOverflow]({{site.so}}/questions/44396075/equivalent-of-relativelayout-in-flutter)
에서 Collin의 답변을 참조하십시오.

### ScrollView와 동일한 것은?

안드로이드에서는 ScrollView를 사용하여 위젯을 배치하는 경우가 있습니다. 
사용자의 기기가 콘텐츠보다 작으면, 스크롤을 해야 합니다.

Flutter에서 가장 쉬운 방법은 ListView 위젯을 사용하는 것입니다.
안드로이드에 익숙하시면 조금 과해 보일 수도 있지만, 
Flutter에서 ListView 위젯은 ScrollView이면서 안드로이드의 ListView입니다.

{% prettify dart %}
  @override
  Widget build(BuildContext context) {
    return ListView(
      children: <Widget>[
        Text('Row One'),
        Text('Row Two'),
        Text('Row Three'),
        Text('Row Four'),
      ],
    );
  }
{% endprettify %}

### Flutter에서 가로 세로 전환을 어떻게 처리합니까?

아래와 같은 속성이 AndroidManifest.xml에 포함되어있는 경우 FlutterView가 환경 설정에 따라 처리합니다.

{% prettify yaml %}
android:configChanges="orientation|screenSize"
{% endprettify %}

## 손동작 감지 및 터치 이벤트 처리

### Flutter에서 위젯에 onClick 리스너를 추가하는 방법은?

안드로이드에서는 'setOnClickListener' 메서드를 호출하여 버튼과 같은 뷰에 onClick을 추가할 수 있습니다.  

Flutter에서는 터치 리스너를 붙이는 2가지 방법이 있습니다:

 1. 위젯이 이벤트 감지를 지원하면, 이벤트를 처리할 수 있는 함수를 전달하세요.
    예를 들어, RaisedButton는 `onPressed` 매개 변수를 가지고 있습니다:

    <!-- skip -->
    ```dart
    @override
    Widget build(BuildContext context) {
      return RaisedButton(
          onPressed: () {
            print("click");
          },
          child: Text("Button"));
    }
    ```

 2. 위젯이 이벤트 감지를 지원하지 않으면, 
    위젯을 GestureDetector로 감싸고 `onTap` 매개 변수에 함수를 전달하세요.

    <!-- skip -->
    ```dart
    class SampleApp extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            body: Center(
          child: GestureDetector(
            child: FlutterLogo(
              size: 200.0,
            ),
            onTap: () {
              print("tap");
            },
          ),
        ));
      }
    }
    ```

### 위젯에서 다른 손동작을 어떻게 처리합니까?

GestureDetector를 활용하여 여러 손동작을 감지할 수 있습니다:

* 탭(Tap)

  * `onTapDown` - 탭의 원인이 될 수 있는 포인터가 화면의 특정 위치에 닿았다. 
  * `onTapUp` - 탭을 발생시킨 포인터가 화면의 특정 위치에 접촉했다가 접촉이 끝났다.
  * `onTap` - 탭이 발생했다.
  * `onTapCancel` - 이전에 `onTapDown`을 발생시켰던 포인터가 탭을 발생시키지 않았다.

* 두 번 탭(Double tap)

  * `onDoubleTap` - 사용자가 화면의 동일한 위치를 빠르게 두 번 탭했다. 

* 길게 누르기(Long press)

  * `onLongPress` - 포인터가 화면의 같은 지점에 오랜 시간 동안 접촉한 채로 남아있다.

* 수직 드래그(Vertical drag)

  * `onVerticalDragStart` - 포인터가 화면에 접촉했고 수직으로 움직이기 시작했다.
  * `onVerticalDragUpdate` - 포인터가 화면에 접촉한 상태로 수직 방향으로 더 움직였다. 
  * `onVerticalDragEnd` - 이전에 화면과 접촉하여 수직으로 움직이던 포인터가
    더 이상 화면과 접촉하지 않고 
    특정 속도로 이동하다가 화면과의 접촉을 멈췄다. 

* 수평 드래그(Horizontal drag)

  * `onHorizontalDragStart` - 포인터가 화면에 접촉했고 수평으로 움직이기 시작했다.
  * `onHorizontalDragUpdate` - 포인터가 화면에 접촉한 상태로 수평 방향으로 더 움직였다. 
  * `onHorizontalDragEnd` - 이전에 화면과 접촉하여 수평으로 움직이던 포인터가
    더 이상 화면과 접촉하지 않고 
    특정 속도로 이동하다가 화면과의 접촉을 멈췄다. 

아래 예제는 두 번 탭(double tap)하면 Flutter 로고를 회전시키는 `GestureDetector`입니다:

{% prettify dart %}
AnimationController controller;
CurvedAnimation curve;

@override
void initState() {
  controller = AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
  curve = CurvedAnimation(parent: controller, curve: Curves.easeIn);
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Center(
          child: GestureDetector(
            child: RotationTransition(
                turns: curve,
                child: FlutterLogo(
                  size: 200.0,
                )),
            onDoubleTap: () {
              if (controller.isCompleted) {
                controller.reverse();
              } else {
                controller.forward();
              }
            },
        ),
    ));
  }
}
{% endprettify %}

## Listviews & adapters

### Flutter에서 ListView 대신 사용할 수 있는 것은?

Flutter에서 ListView와 동일한 것은… ListView입니다!

안드로이드 ListView에서는 어댑터를 작성하여 ListView로 전달하면,
어댑터가 반환하는 대로 각 행을 렌더링합니다.
하지만 행을 꼭 재활용하도록 하세요.
그렇지 않으면 온갖 미친 시각적 결함이나 메모리 문제가 생깁니다.

Flutter의 불변 위젯 패턴 덕분에,
ListView에 위젯 리스트를 전달하면 Flutter는 스크롤이 빠르고 부드럽게 처리합니다.

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: ListView(children: _getListData()),
    );
  }

  _getListData() {
    List<Widget> widgets = [];
    for (int i = 0; i < 100; i++) {
      widgets.add(Padding(padding: EdgeInsets.all(10.0), child: Text("Row $i")));
    }
    return widgets;
  }
}
{% endprettify %}

### 리스트에서 어떤 항목을 클릭했는지 어떻게 알 수 있나요?

안드로이드에서는 ListView가 어떤 항목을 클릭했는지 알려주는 'onItemClickListener'라는 메서드가 있습니다.
Flutter에서는 전달된 위젯에서 터치를 제어합니다.

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: ListView(children: _getListData()),
    );
  }

  _getListData() {
    List<Widget> widgets = [];
    for (int i = 0; i < 100; i++) {
      widgets.add(GestureDetector(
        child: Padding(
            padding: EdgeInsets.all(10.0),
            child: Text("Row $i")),
        onTap: () {
          print('row tapped');
        },
      ));
    }
    return widgets;
  }
}
{% endprettify %}

### ListView를 어떻게 동적으로 업데이트합니까?

안드로이드에서는 어뎁터를 수정하고 `notifyDataSetChanged`를 호출합니다.

Flutter에서는 `setState()` 안에서 위젯의 리스트를 수정하면, 데이터가 변경되지 않았음을 시각적으로 빠르게 확인할 수 있다.
`setState()`가 호출되면 Flutter의 랜더링 엔진이 변경 사항이 있는지 위젯 트리를 확인하기 때문입니다.
랜더링 엔진이 `ListView`까지 오면, 
`==` 방식의 검사를 하고, 두 `ListView`가 같은지 체크합니다.
아무것도 안 바뀌었으면, 업데이트가 필요 없습니다.

간단하게 `ListView`를 업데이트하려면, 
`setState()` 안에서 새로운 `List`를 만들고 이전 리스트를 새로운 리스트로 복사하세요.
이 방법은 간단하긴 하지만, 아래 예제 처럼 큰 데이터를 다룰 때 권장할만한 방법은 아닙니다.

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = <Widget>[];

  @override
  void initState() {
    super.initState();
    for (int i = 0; i < 100; i++) {
      widgets.add(getRow(i));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: ListView(children: widgets),
    );
  }

  Widget getRow(int i) {
    return GestureDetector(
      child: Padding(
          padding: EdgeInsets.all(10.0),
          child: Text("Row $i")),
      onTap: () {
        setState(() {
          widgets = List.from(widgets);
          widgets.add(getRow(widgets.length + 1));
          print('row $i');
        });
      },
    );
  }
}
{% endprettify %}

리스트를 만들 때 권장되고 효율적이며 효과적인 방법은 ListView.Builder를 사용하는 것입니다.
이 메서드는 동적인 리스트를 다뤄야 하거나 많은 데이터를 다뤄야 할 때 매우 좋습니다. 
리스트 요소를 재활용 한다는 점에서 본질적으로 안드로이드의 RecyclerView와 동일합니다.

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  List widgets = <Widget>[];

  @override
  void initState() {
    super.initState();
    for (int i = 0; i < 100; i++) {
      widgets.add(getRow(i));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text("Sample App"),
        ),
        body: ListView.builder(
            itemCount: widgets.length,
            itemBuilder: (BuildContext context, int position) {
              return getRow(position);
            }));
  }

  Widget getRow(int i) {
    return GestureDetector(
      child: Padding(
          padding: EdgeInsets.all(10.0),
          child: Text("Row $i")),
      onTap: () {
        setState(() {
          widgets.add(getRow(widgets.length + 1));
          print('row $i');
        });
      },
    );
  }
}
{% endprettify %}

"ListView"를 만드는 대신 ListView.builder를 만듭니다.
ListView.builder는 리스트의 초기 길이와 ItemBuilder 함수라는 
2가지 주요 매개 변수를 가지고 있습니다.

ItemBuilder 함수는 안드로이드 어뎁터의 `getView` 함수와 유사합니다.
position을 받아서 랜더링이 필요한 해당 row를 반환합니다.

마지막으로 가장 중요한 작업이 남았습니다. 
`onTap()` 함수가 리스트를 다시 생성하는 대신 `.add`를 사용해 요소를 추가하게 합니다.  

## 텍스트 작업하기

### 텍스트 위젯에서 사용자 지정 글꼴을 설정하는 방법은?

안드로이드 SDK에서 (안드로이드 O 기준),
글꼴 리소스 파일을 만들고 TextView의 FontFamily 매개 변수로 전달합니다.

Flutter에서는 이미지 가져오는 방법과 비슷하게
글꼴 파일을 폴더에서 넣고 `pubspec.yaml`에서 글꼴 파일 위치를 지정합니다.  

{% prettify yaml %}
fonts:
   - family: MyCustomFont
     fonts:
       - asset: fonts/MyCustomFont.ttf
       - style: italic
{% endprettify %}

그런 다음 `Text` 위젯에 폰트를 지정하세요:

{% prettify dart %}
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text("Sample App"),
    ),
    body: Center(
      child: Text(
        'This is a custom font text',
        style: TextStyle(fontFamily: 'MyCustomFont'),
      ),
    ),
  );
}
{% endprettify %}

### 텍스트 위젯을 꾸미는 방법은?

글꼴 이외에도 다양한 방법으로 `Text` 위젯을 커스터마이징할 수 있습니다.
`Text`의 style 매개 변수에서는 `TextStyle` 객체를 사용하는데,
`TextStyle`에는 아래와 같은 다양한 매개 변수가 있습니다. 

* color
* decoration
* decorationColor
* decorationStyle
* fontFamily
* fontSize
* fontStyle
* fontWeight
* hashCode
* height
* inherit
* letterSpacing
* textBaseline
* wordSpacing

## 양식 입력(Form input)

Form 사용법에 관한 자세한 방법은 
[Flutter Cookbook](/docs/cookbook)의
[텍스트 필드 값 가져오기](/docs/cookbook/forms/retrieve-input)를
참조하세요.

### Input에서 "hint"와 동일한 것은? 

Flutter에서는
텍스트 위젯의 생성자 매개 변수로 
InputDecoration 객체를 추가하여
쉽게 "hint"나 placeholder를 보여줄 수 있습니다.

{% prettify dart %}
body: Center(
  child: TextField(
    decoration: InputDecoration(hintText: "This is a hint"),
  )
)
{% endprettify %}

### 검증(validation) 오류를 보여주는 방법은?

"hint"와 마찬가지로,
텍스트 위젯 생성자의 decoration에 
InputDecoration 객체를 넘기면 됩니다.

하지만,
처음부터 에러를 보여주고 싶지는 않을 겁니다.
그 대신,
사용자가 유효하지 않은 값을 입력했을 때
상태를 업데이트하여 새로운 `InputDecoration`를 전달하세요.

{% prettify dart %}
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  String _errorText;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: Center(
        child: TextField(
          onSubmitted: (String text) {
            setState(() {
              if (!isEmail(text)) {
                _errorText = 'Error: This is not an email';
              } else {
                _errorText = null;
              }
            });
          },
          decoration: InputDecoration(hintText: "This is a hint", errorText: _getErrorText()),
        ),
      ),
    );
  }

  _getErrorText() {
    return _errorText;
  }

  bool isEmail(String em) {
    String emailRegexp =
        r'^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$';

    RegExp regExp = RegExp(emailRegexp);

    return regExp.hasMatch(em);
  }
}
{% endprettify %}


## Flutter 플러그인

### GPS 센서에 접근하는 방법은?

커뮤니티 플러그인인 [`geolocator`]({{site.pub}}/packages/geolocator)를 사용하세요.

### 카메라에 접근하는 방법은?

[`image_picker`]({{site.pub}}/packages/image_picker) 플러그인은
카메라에 접근할 때 많이 사용됩니다.

### 페이스북으로 로그인하는 방법은?

페이스북으로 로그인하기 위해, 커뮤니티 플러그인인
[`flutter_facebook_login`]({{site.pub}}/packages/flutter_facebook_login)을 사용하세요.

### Firebase 기능을 사용하는 방법은?

대부분의 Firebase 기능은 
[first party plugins]({{site.pub}}/flutter/packages?q=firebase)
를 사용하여 다룰 수 있습니다.
이 플러그인들은 Flutter 팀에서 관리하는 자사 통합 플러그인입니다: 

 * Firebase AdMob을 위한 [`firebase_admob`]({{site.pub}}/packages/firebase_admob)
 * Firebase 애널리틱스를 위한 [`firebase_analytics`]({{site.pub}}/packages/firebase_analytics)
 * Firebase 인증을 위한 [`firebase_auth`]({{site.pub}}/packages/firebase_auth)
 * Firebase 실시간 데이터베이스를 위한 [`firebase_database`]({{site.pub}}/packages/firebase_database)
 * Firebase Cloud Storage를 위한 [`firebase_storage`]({{site.pub}}/packages/firebase_storage)
 * Firebase 메시징 (FCM)을 위한 [`firebase_messaging`]({{site.pub}}/packages/firebase_messaging)
 * 빠르게 Firebase 인증(Facebook, Google, Twitter and email)을 설정하기 위한 [`flutter_firebase_ui`]({{site.pub}}/packages/flutter_firebase_ui)
 * Firebase Cloud Firestore를 위한 [`cloud_firestore`]({{site.pub}}/packages/cloud_firestore)

또한 자사 통합 플러그인로 구현이 어려운 부분이 있다면 Pub에서 Firebase관련 서드 파티 플러그인을 찾아 사용하실 수 있습니다.

### 네이티브와 직접 통합하고 싶을 때는? 

Flutter 또는 커뮤니티 플러그인이 놓친 부분이 있다면,
[패키지 및 플러그인 개발](/docs/development/packages-and-plugins/developing-packages)을 참고하여
직접 플랫폼 특화 기능을 구현할 수 있습니다.

Flutter의 플러그인 구조는 쉽게 말하면 안드로이드의 이벤트 버스와 비슷합니다:
메시지를 보내고, 수신자가 처리한 뒤 결과를 다시 보내도록 하는 방식입니다.
이 경우, 수신자는 안드로이드 또는 iOS의 네이티브 쪽에서 실행되는 코드에 해당합니다.

### Flutter 앱에서 NDK를 사용하는 방법?

현재 안드로이드 앱에서 NDK를 사용하고 있고 Flutter 앱에서도 네이티브 라이브러리의 이점을 살리고 싶다면
직접 플러그인을 만드실 수 있습니다.

직접 만든 플러그인은 일단 안드로이드 앱을 호출한 뒤, JNI를 통해 `네이티브` 함수를 호출합니다.
응답이 준비되면 Flutter 쪽으로 응답을 보낸 후 결과를 보여주면 됩니다. 

Flutter에서 직접 JNI를 사용하는 방법은 현재 지원하고 있지 않습니다.

## 테마

### 앱에 테마를 설정하는 방법은?

Flutter는 일반적으로 많이 사용하는 스타일링과 테마를 처리할 수 있는 
아름다운 머티리얼 디자인 구현을 제공하고, 즉시 사용가능합니다.
AndroidManifest.xml를 사용하여 XML 방식으로 테마를 설정하는 안드로이드와 달리,
Flutter에서는 최상위 위젯에서 테마를 설정합니다.

앱에서 머티리얼 컴포넌트의 이점을 살리기 위해서,
앱의 진입점으로 최상위 위젯 `MaterialApp`을 설정할 수 있습니다.
MaterialApp은  
여러 일반적으로 사용되는 머티리얼 디자인 위젯을 담고 있어
편리하게 머티리얼 디자인을 구현할 수 있도록
도와주는 위젯입니다.  
MaterialApp은 WidgetsApp을 기반으로 머티리얼 특유의 기능을 추가하여 구현됐습니다.

`WidgetApp`을 앱 위젯으로 사용하셔도 됩니다.
`MaterialApp`과 동일한 기능을 제공하지만, `MaterialApp`만큼 풍성하지는 않습니다.

자식 컴포넌트에 색과 스타일을 커스터마이징하려면,
`ThemeData` 객체를 `MaterialApp` 위젯에 전달하세요.
예를 들어, 아래 코드처럼
primary swatch를 파란색으로 설정하고 
text selection color를 빨간색으로 설정할 수 있습니다. 

{% prettify dart %}
class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        textSelectionColor: Colors.red
      ),
      home: SampleAppPage(),
    );
  }
}
{% endprettify %}


## 데이터베이스 및 로컬 저장소

### Shared Preferences에 접근하는 방법은?

안드로이드에서는 적은 양의 키-값을 저장하기 SharedPreferences API를 사용할 수 있습니다.

Flutter에서는 같은 기능을
[Shared_Preferences 플러그인]({{site.pub}}/packages/shared_preferences)을
통해 사용할 수 있습니다.
이 플러그인은 Shared Preferences와 NSUserDefaults(iOS)의 기능을 감싸고 있습니다.

{% prettify dart %}
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() {
  runApp(
    MaterialApp(
      home: Scaffold(
        body: Center(
          child: RaisedButton(
            onPressed: _incrementCounter,
            child: Text('Increment Counter'),
          ),
        ),
      ),
    ),
  );
}

_incrementCounter() async {
  SharedPreferences prefs = await SharedPreferences.getInstance();
  int counter = (prefs.getInt('counter') ?? 0) + 1;
  print('Pressed $counter times.');
  prefs.setInt('counter', counter);
}

{% endprettify %}

### Flutter에서 SQLite에 접근하는 방법?

안드로이드에서는 SQLite을 사용하여 구조적인 데이터를 저장하고
SQL을 사용해 쿼리할 수 있습니다.

Flutter에서는, 
[SQFlite]({{site.pub}}/packages/sqflite) 플러그인을 활용해 
이 기능에 접근합니다.

## 알림

### 푸시 알림을 설정하는 방법은?

안드로이드에서는 앱에 푸시 알림을 설정하기 위하여 
Firebase Cloud Messaging을 사용합니다.

Flutter에서는 
[Firebase_Messaging]({{site.github}}/flutter/plugins/tree/master/packages/firebase_messaging) 플러그인을
활용하여 이 기능에 접근합니다.
Firebase Cloud Messaging API에 대한 저 자세한 정보는
[`firebase_messaging`]({{site.pub}}/packages/firebase_messaging)
플러그인 문서를 참조하세요.
