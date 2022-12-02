# How to think about WASM ?
* [How to think about wasm](https://www.fermyon.com/blog/how-to-think-about-wasm) 글을 옮겨놓은 내용이며, 중간 중간 개인적인 의견이 포함되어있습니다.
* 웹어셈블리에 대한 깊이 있는 내용을 다루지는 않았고, 나온지 한참된 웹어셈블리가 요즘 왜 뜨거운지 생각해 본 내용입니다.



## :balloon: 요즘 웹어셈블리에 대한 이야기가 많이 나오고 있습니다. 
2017년, 지금으로부터 5년전 등장한 웹어셈블리(WebAssembly, [WASM](https://en.wikipedia.org/wiki/WebAssembly))이 요즘에는 웹을 넘어, 컨테이너 다음의 클라우드 차세대 기술로 많이 이야기되고 있는 듯 합니다.(아직은 과장이 많이 있는 것 같지만 아무튼) 아래와 같이 웹어셈블리를 이야기하는 사람들이 있고, 그 중 가장 잘 알려진 이야기중 하나가 도커의 공동 창립자인 Solomon Hykes가 2019년에 아래 트윗을하면서 사람들의 관심을 더 불러 일으킨 것 같습니다.

> If WASM+WASI existed in 2008, we wouldn't have needed to created Docker. That's how important it is. Webassembly on the server is the future of computing. A standardized system interface was the missing link. Let's hope WASI is up to the task! 

> "2008년에 WASM와 WebAssembly System Interface가 있었다면, 도커를 만들 필요가 없었을 겁니다..." @도커 공동 창립자 Solomon Hykes

* "자바스크립트의 킬러" [...the JavaScript Killer](https://www.slideshare.net/bmihaylov/is-webassembly-the-killer-of-javascript)
* "웹을 위한 새로운 프로그래밍 언어" [...new programming language for the web](https://www.tutorialspoint.com/webassembly/index.htm)
* "클라우드 컴퓨팅의 새로운 물결" [...the next wave of cloud compute](https://www.fermyon.com/blog/2022-02-08-hello-world)
* "더 나은 e버클리패킷필터" [...a better eBPF](https://marcusparadies.github.io/files/ebpf_vs_wasm_report.pdf)
* "리스크 파이브의 대안" [...alternative to RISC V](https://medium.com/@losfair/a-comparison-between-webassembly-and-risc-v-e8fb9d37e6cc)
* "자바의 경쟁 상대 [...a competitor to Java](https://steveklabnik.com/writing/is-webassembly-the-return-of-java-applets-flash)
* "브라우저의 성능 부스터" [...a performance booster for browsers](https://www.opensourceforu.com/2018/04/webassembly-to-speed-up-performance-of-web-browsers/)
* "도커의 대체" [...a replacement for Docker](https://kubesphere.io/blogs/will-cloud-native-webassembly-replace-docker_/)
* 라고들 하지만 하지만 과연 맞는 말일까? 판단이 어렵습니다.

사실 저는 웹어셈블리 조차도 낯설기 때문에 웹어셈블리가 무엇인지 부터 알아야 했습니다.:sleeping: 자 그럼 웹어셈블리 먼저 알아 볼까요?(다 아시는 분들은 패쓰:blush:)

## :question: 그럼, 웹어셈블리는 뭘까요? 
구글링 해 보면 보통 이런 문장으로 설명합니다. "WebAssembly is as a standardized bytecode format for executing programs / 프로그램 실행을 위한 표준화된 바이트코드 형식" 참 간단 명료하지만 잘 이해가 안가는 표현이었습니다.(아래처럼 이해하니 감이 조금은 왔습니다.)

프로그래밍 언어 몇 가지로 예를 들어,
* C, C++, Go, Rust 같은 언어들은: 소스코드를 리눅스, 윈도우즈 등 운영체제에 맞게 바이트코드로 컴파일을 하면, 각 운영체제에 맞는 실행가능한 바이너리가 생기고 해당 파일로 프로그램이 실행되는 형태 입니다.
* Python, JavaScript, Ruby 같은 언어들은: C 처럼 컴파일 과정이나 별도의 바이너리가 없이 소스코드를 빠르게 읽어서 프로그램이 실행되는 형태입니다. 아무래도 좀 느리겠죠
* Java, C#과 같은 언어들은: C 와 같이 소스코드를 바이트코드 형식으로 컴파일하고 바이너리를 얻는건 같지만 운영체제에서 바로 실행되는 것이 아닌, 해당 바이너리가 실행되기 위한 특별한 환경이 필요한 경우입니다. 컴파일된 바이너리가 실행되기 위해서 Java의 경우 JVM(Java Virtual Machine), .NET의 경우 CLR(Common Language Runtime)이라는 별도의 환경에서 실행되는 형태가 있습니다.

이런 프로그래밍 언어의 특징으로 볼 때 웹어셈블리라는 것은
* C 처럼 소스코드가 바이트코드로 컴파일되는 형태 이면서,
* Java 처럼 컴파일된 바이너리가 실행되기 위해 특수한 환경이 필요한 형태로 이야기 할 수 있겠습니다.
* 즉, 웹어셈블리는 '웹어셈블리 런타임이라는 특수한 환경에서 실행되는 바이트코드 형식의 프로그램' 이라고...
* (그래도 좀 어려운데.. 전 "웹어셈블리 런타임을 우리가 쓰는 웹브라우저로, 운영체제나 아키텍처에 상관 없이 웹브라우저만 있으면 실행가능한 바이너리? 표준바이트코드?" 정도로 웹어셈블리를 이해했습니다.)

그럼, 이 웹어셈블리는 어떻게 시작되었을까요? 

## :sunny: 원래 시작은 웹이었고 요즘이야기는 아니었습니다.
웹어셈블리를 '웹어셈블리 런타임에서 실행 가능한 바이트코드 형식'으로 이해했는데, 그럼 '웹어셈블리 런타임' 이라는 것은 어떤 것을 의미할까요? 우리가 사용하는 크롬이나 사파리 같은 웹브라우저로 보면 되겠습니다.  '웹'어셈블리의 시작은 원래 웹에 관한 것이었고, 웹브라우저에서 많이 사용하는 JavaScript 말고도 컴파일된 바이너리도 실행할 수 있게 하는 것이 웹어셈블리의 시작이었습니다.(브라우저에 .wasm 바이너리가 실행될 수 있는 환경 집어넣기) 당연한 이야기 이겠지만 웹 성능 향상이 이유였을 것 같습니다. 아무래도 스크립트 언어보다는 바이너리가 빠를 테니까요. (← 여기서 일부 사람들이 웹어셈블리를 자바스크립트의 킬러라고 말하는 이유를 조금 이해할 수 있습니다. 많이 과장된 표현이지만요)

웹어셈블리 런타임으로 사용하는 브라우저의 특성은 아래와 같습니다.(갑자기 브라우저의 특성을 말씀 드리는 이유는 웹어셈블리가 웹을 넘어 클라우드로 이야기 되는 공통점이 있기 때문입니다. 이부분은 뒤에 말씀드리기로 하고) 브라우저의 특성은

* 강력한 보안: 인터넷 어딘가에서 가져온 임의의 코드는 신뢰할 수 없습니다.
* 작은 바이너리: 인터넷 대역폭에 제약이 있는 곳은 분명 있을테니 가능한 수 KB 정도로 작아야 합니다.
* 빠른 로딩: 빠르게 로딩되어야 사용자들이 브라우저를 떠나지 않습니다.
* 다양한 운영체제 및 아키텍처 호환: 다양한 운영체제와 기기사용에 있어 제약이 있으면 안됩니다.
* 브라우저 내 상호 운용성: 브라우저에서 바이너리를 실행하는 것만으로 충분하지 않고, 바이너리와 기존 HTML, CSS와의 내부 호환성도 중요합니다.

## :cloud: 웹어셈블리, 더이상 웹 만의 것은 아닙니다. 웹을 넘어 클라우드로
아마존이 자체 데이터센터 서버들로 클라우드 컴퓨팅 장사를 시작한 배경은 다들 알고 계실테니 생략하고, 컴퓨팅 파워를 임대하는 일은 상당히 복잡하고 까다로운 요구사항이 있습니다. (더 많겠지만 아래 정도?)

* 강력한 보안
* 작은 바이너리
* 빠른 로딩
* 다양한 운영체제 및 아키텍처 호환
* 클라우드 내부서비스와의 상호 운용성

(사실 억지로 끼워 맞춘 듯한 느낌이 좀 들지만) 데이터 센터에 있는 서버로 컴퓨팅 임대 사업을 할 때 있을 수 있는 요구사항인 것은 맞는 것 같습니다. 자. 여기서 웹어셈블리 런타임에 해당하는 브라우저의 요구사항과 클라우드의 요구사항과 비슷한 것을 볼 수 있습니다. "아 그래? 그럼 웹어셈블리 런타임을 그냥 사용자 웹브라우저로 사용하는게 아닌, 클라우드 컴퓨팅을 제공하는데 사용하면 클라우드 컴퓨팅 요구사항을 향상시킬 수 있는 것 아냐?"  (← 여기서 사람들이 웹어셈블리를 클라우드 컴퓨팅의 새로운 물결 이라는 이유를 조금 이해할 수 있었습니다.)

아시겠지만 클라우드에서의 컴퓨팅 자원에 대한 임대는 가상머신부터 시작되었습니다. 가상머신, 어느 정도 크기도 작아졌지만 확실하게 작지는 않았고, 보안도 최고 정점은 아니었고, 시작 시간도 많이 빠르지는 않았습니다. 이렇게 가상머신으로 시작한 클라우드 컴퓨팅은 컨테이너가 대중화되면서 가상머신과는 조금 다른 선택지가 생겼습니다. 2013년 도커가 컨테이너를 대중화 시켰고 컨테이너는 가상머신보다 더 작은 크기, 더 빠른 로딩으로 수년만에 가상머신과 함께 새로운 컴퓨팅 클래스가 되었습니다. 또 2015년 쿠버네티스와 같은 시스템은 컨테이너와 다른 클라우드 서비스와의 상호 운용성을 체계화 시켰습니다.(진정한 클라우드네이티브로) 사실 오늘날 가상머신으로만 컴퓨팅 자원을 임대하는 클라우드 제공 업체는 없으니 어느정도 맞는 말인 것 같습니다.

다만, 컨테이너만으로도 아직은 부족합니다. (서버리스 같은 것을 위해서라면 더욱)더 작은 바이너리, 더 빠른 시작시간, 또 운영체제와 아키텍처에 종속적이지 않는 실행 환경이 필요했고, 이런 부분이 웹어셈블리의 특성, 아니 웹어셈블리 런타임이 갖는 특성과 유사하기 때문에 클라우드에서 웹어셈블리가 핫 해지는 것 같습니다. 클라우드 컴퓨팅 제공방식에 가상머신이 헤비급, 컨테이너가 미들급 정도라면 웹어셈블리를 라이트급 정도로 생각할 수 있겠습니다. 

크롬 웹브라우저에서 서버없이 웹어셈블리 파일로 데이터베이스를 올리고, 게임을 실행시키는 등등의 사례 들이 있는 것 같습니다. 개인적으로 처음에는 이런 것을이 왜 필요한지, 이런 것들이 어떤 의미가 있는지 회의적이었습니다.(그래서 뭐? 정도의 느낌) 하지만 글을 정리하면서 웹어셈블리가 핫해지는 이유를 아래로 이해하게 됐습니다. 

:pushpin: 웹어셈블리가 클라우드에서 핫한 이유를 :heavy_check_mark:   
* 웹브라우저에서 .wasm 파일 만으로 무언가를 빠르게 할 수 있기 때문이 아니라(태생도 이랬고, 신기하기도하지만)
* 웹브라우저와 .wasm 파일만 있으면 실행되는 이 웹어셈블리 환경을
* (웹어셈블리와 클라우드 컴퓨팅의 요구사항이 같으니)
* 즉 웹어셈블리 런타임을 클라우드 컴퓨팅의 제공방식으로 가져와서 사용한다면(WASI) 
* 가상머신, 컨테이너, 그 다음의 컴퓨팅 제공 클래스로 사용할 수 있을 거라는 기대 때문에
* 사람들의 관심을 모으는 것이 아닐까 생각해 봤습니다.

## :tada: 그래서 요즘은 어떤일이 일어나고 있을까요
가상머신, 컨테이너, 그리고 그 이후 기술로 떠오르고 있는 웹어셈블리에 대한 주도권? 기술선도?를 위해 여러 일들이 벌어지고 있는 것 같습니다. 

Introducing the Docker+Wasm Technical Preview, Oct 2022
* 컨테이너를 대중화했던 도커가 WASM도 제공하기 시작했습니다.
* https://www.docker.com/blog/docker-wasm-technical-preview/
AKS support for WebAssembly System Interface (WASI) workloads, Oct 2021
* Microsoft Azure AKS 에서 krustlet(rust 기반의 kubelet)으로 WASM을 제공하기 시작했습니다. 
* https://azure.microsoft.com/ko-kr/updates/public-preview-aks-support-for-webassembly-system-interface-wasi-workloads/
Fermyon launched its microservice-oriented platform for WebAssembly apps, called Fermyon Cloud, Nov 2022
* Fermyon 에서 오픈소스 Spin 기반의 웹어셈블리 플랫폼을 출시했습니다.
* https://www.infoq.com/news/2022/11/Fermyon-cloud-webassembly/
CNCF에서 호스팅하는 WASM 프로젝트 들이 생겨났습니다. 
* https://www.cncf.io/blog/2021/08/05/cloud-native-webassembly/
또 더 있을 것 같은데.. 일단.

## :pray:마치며
웹어셈블리는 "프로그램 실행을 위한 표준화된 바이트코드 형식"으로 웹 만을 위해 시작 되었지만, 최근에는 그 요구사항들이 클라우드 컴퓨팅 자원 제공의 요구사항과 공통점이 많기 때문에 재 조명되고 있는 것 같습니다. 우리가 수 년전 도커와 쿠버네티스를 만났고 컨테이너와 쿠버네티스가 아직은 대세인 요즘이지만 여전히 가상머신은 자기 자리가 있고 계속 사용되고 있는걸 보면. 웹어셈블리가 대세가 되어도 완전한 대체가 아닌 또 하나의 선택지 정도가 될 듯 합니다. (뭐 또 이러다가 그냥 시들해 질 수도 있겠지만) 새로운 기술 트랜트를 잘 지켜보는 것은 중요하다고 생각합니다.(계속 따라가다 보면 언젠가 또 하나 걸리겠죠(함박 미소)) 웹어셈블리. 사실 더 깊이 있는 내용이 많겠지만 일단은 과연 뭐하는 물건인지, 왜 핫 한지 정도를 함께 알면 좋을 것 같아 얕은 지식으로 공유드려 봅니다. 고맙습니다. 
