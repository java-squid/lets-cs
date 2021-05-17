# web-cache

- 클라이언트, 서버 사이드에서 사용하는 용도로 나뉘어져 있음.
- 이미지 혹은 다른 파일들을 캐싱하는 것은, web page를 브라우징 하는 데 걸리는 딜레이를 줄여줌



## 종류

1. Browser cache
2. Proxy cache
3. Gateway cache



## HTTP 1.1 cache-control

- 이 옵션을 통해 다양한 조건을 설정할 수 있음.
- max-age=[sec]
- no-cache, no-store
- private 등등..



## 동작 방법

1. 클라이언트가 서버에 리소스를 요청하고,
2. 서버가 클라이언트의 요청을 검토하여, 있으면 해당 리소스와 헤더값을 응답함.
3. 클라이언트는 응답 받은 자원을 그리고, 응답 헤더에 따라 캐시 정책을 수행



### 응답 헤더에...

1. LAST-MODIFIED

![](https://t1.daumcdn.net/cfile/tistory/2453A54F558225240E)



- 요청 보낼 때, `if-modified-since` 를 보내서, 서버는 이를 기반으로 해당 파일이 변경되었는지 확인하고
- 변경되지 않았다면 304 Not modified 응답
- 변경 되었다면 200과 새로운 파일 , Last modified 값을 헤더에 넣어 전송
- 클라이언트는 304 일 경우, 캐시된 페이지를 로드하고, 200일 경우 새로 다운 받은 리소스를 캐시 한다음에, Last modified 값을 갱신..



2. ETAG 

- 1번과 비슷한 체크 과정을 거침



3. Expires

- Expire 값과 비교하여, 기간 내라면 캐시에서 바로 로드 되도록.
- 기간이 지났다면 서버에게 요청이 가서, validation이 체크됨.



4. Cache-control

- max-age를 GMT와 비교, 기간 내라면 바로 캐시에서 바로 로드 됨.
- `Expires` 보다 우선 됨.



# 참고

- https://en.wikipedia.org/wiki/Web_cache
- https://hahahoho5915.tistory.com/33
- https://developer.mozilla.org/ko/docs/Web/HTTP/Caching

