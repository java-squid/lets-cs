# Web Cache

캐시는 프록시 서버와 클라이언트의 로컬 디스크에 보관된 리소스의 사본을 가리킵니다. 캐시 서버를 사용하면 예들들어 일정 시간동안은 실제 서버에 방문해서 리소스를 가져오는 것이 아니라 캐시 서버에서 리소스 사본을 응답하는 방식으로 실서버의 통신량과 통신 시간을 절약할 수 있는 이점이 있습니다.

**(이용자 기준에서 실제 서버보다 캐시 서버의 거리가 더 가깝기 때문에 더욱 빠른 응답을 할 수 있습니다.)**

**[프록시 서버는 무엇일까 ? 🧐](https://namu.wiki/w/%ED%94%84%EB%A1%9D%EC%8B%9C%20%EC%84%9C%EB%B2%84)**

`클라이언트` < - > `캐시 서버` < - > `실서버`

캐시 서버는 프록시의 기본 동작을 따르지만 큰 구분점은 실제 서버에서 받은 리소스를 자신의 디스크 또는 메모리에 저장한 후 클라이언트에게 제공한다는 차이점이 있습니다.

**캐시의 유효기간**

캐시 서버에 캐시된 리소스가 있는 경우라도 계속해서 캐시된 리소스를 제공하는건 아닙니다. 서버는 여러 리소스를 가지고 있으며 캐시 서버는 실제 서버의 사본에 불과합니다. 만약에 css 또는 png 같은 이미지 리소스 형식에 변경이 있다면 실제로 사용자는 화면이 깨진 형태 또는 이미지 리소스를 받을수도 있습니다. 따라서 캐시 서버는 캐싱된 리소스의 유효성을 주기적으로 확인하고 업데이트 여부를 확인하여 업데이트가 있다면 새로운 리소스를 받는 과정이 필요합니다.

이 부분을 설명하기 위해서는 캐시 서버의 리소스 유효성에 대해서 설명이 필요합니다.

**최초 컨텐츠 요청**

`클라이언트` < - > `캐시 서버` < - > `실서버`

- 클라이언트가 웹 브라우저에 이미지 리소스를 요청한다.
- 캐시 서버에 도달했을 때 캐시 서버는 캐시된 리소스가 없는걸 확인하고 실서버에 리소스를 요청한다.
- 실서버는 리소스와 함께 캐싱 유효성 , 기간을 판단하는 메타 태그 정보를 함께 전달한다.

**last-modified :** **가장 마지막으로 업데이트 된 시기**

    **cache-control : 캐시 리소스의 유효 기간 해당 시간이 지났다면 캐시 서버는 실서버에 재요청하여 리소스를 업데이트**



    **cache-control 설명**

    **public : HTTP 응답 내에서 서버에 의해 사용될 수 있는 표준 Cache-Control 디렉티브를 의미합니다. public은 콘텐츠가 브라우저 및 중간 캐시에 의해 캐시 될 수 있음을 나타냅니다. HTTP 인증을 사용하는 요청에 대한 기본 개인 설정을 재정의합니다. 비공개와 상호 배타적입니다.**

    **immutable : 웹 브라우저는 max-age가 만료되지 않더라도 새로고침 시  캐시의 유효성을 검증하기 위한 캐시 검증을 요청합니다. immutable 속성은 max-age는 파일이 변동되지 않을것이라고 예측하고 검증의 과정을 거치지 않습니다. 이런 옵션은 좀 더 정확한 인증 과정 보다는 캐시 서버의 최적화에 도움이 되는 옵션입니다.**

    **max-age : 캐시 서버에 리소스를 사용하는 만료 시간입니다. 위와 같이 3153600의 값은 max-age를 설정할 수 있는 최대치인 1년을 표현하고 있습니다.**

- 클라이언트는 위와 같은 정보들을 캐시 서버로 부터 수신 후 사용자에게 제공한다.

**리소스를 다시 요청하는 순간에 cache-control의 max-age는 변동한다.**

말그대로 만료 시간이 120ms로 지정된 cache-control 에 30초 이후에 다시 요청한다면 해당 메타 태그의 값은 120 - 30 = 90의 값으로 응답이 돌아오게 됩니다.
