## nginx 팁

nginx 플로우를 정확하게 파악해야 트러블슈팅을 할 수 있음

proxy_pass 추가하여 L7 로드밸런싱

supervisor는 health check 기능이 있다.

api 도메인 따로 만들기

http -> /etc/nginx/nginx.conf -> /etc/nginx/sites-enabled/* -> servers(api, front)

servers(front) -> server(80,443) -> server(443) -> /dist (build) -> index.html -> servers(api)

servers(api) -> server(80,443) -> server(443) -> static, media, proxy_pass {function_name}:upstream -> server unix:{SOCKFILE:gunicorn}.sock (SOCKFILE == /gunicorn {djangoFolder}.wsgi:application) -> supervisor

supervisor -> /etc/supervisor/conf.d/{configFileName}.conf -> [program:{filename}] -> gunicorn -> SOCKFILE:gunicorn

### certbot fullkeychain 오류

unlink /etc/nginx/sites-enabled/{file-name}

### service nginx restart " " in ~ 오류

들여쓰기는 탭만 인식함

### 포트 사용중일때

sudo apt install net-tools(for netstat)

fuser -k -n tcp 80 (kill some port)

### 로드밸런서 종류

- L2 : Data link 계층을 사용, Mac주소 기반 부하 분산
- L3 : Network 계층을 사용, IP주소 기반 부하 분산
- L4 : Transport 계층을 사용, Port 기반 부하 분산
- L7 : Application 계층을 사용, 요청(URL) 기반 부하 분산

### bad requests 400 관련

settings.py의 allow hosts는 http 포함하지 않은 도메인으로

### certbot sites-enabled 관련

링크를 걸기 전에 certbot으로 key를 만들어줘야된다.

## Vue deploy 에러

api를 안 거치면 글자가 전체적으로 깨진다. 배포 서버의 /src/main.js.axios.defaults.baseURL가 api 도메인으로 통하는지 확인

https://stackoverflow.com/questions/63564863/how-to-fix-vue-js-error-405-not-allowed-in-production-mode-used-with-nginx-sym

## Lazy loding, Eager loding

장고 orm은 데이터를 실제로 캐싱(파이썬 자료구조로 형변환)할 때 데이터를 db에서 꺼내옴. 이를 Lazy 로딩이라고 함. 
N+1문제때문에 Eager로딩으로 바꿔야 할 수도 있는데, 
select_related와 prefetch_related로 해결할 수 있다.

### select_related, prefetch_related

select_related 1:1 또는 1:N에서 사용하고, db에서 join하는 방식으로 정방향 참조(N->1 방향)시 유리하다. 
prefetch_related N:1 또는 M:N에서 사용하고, 추가로 쿼리문을 날려 파이썬에서 join하는 방식으로 역방향 참조(1->N 방향)시 유리하다.

## N+1문제

queryset은 _result_cache에 dict로 데이터셋을 멤버 변수로 가진 오브젝트로 반환된다.
lazy loding시 명시된 오브젝트가 실행된 경우 쿼리를 날리는데, 오브젝트가 실행되지 않으면 쿼리를 날리지 않는다.
이 때 queryset에 쿼리가 캐싱된 줄 알고 조건절로 1개의 쿼리를 반복하여 날리면 _result_cache에 데이터가 없어 쿼리를 하나씩 날린다.
이를 해결하기 위해 Eager 로딩으로 데이터셋을 전부 끌어온다.

## 장고 inner join, outer join

foreignkey로 접근했을 때 null 파라미터의 bool

## DEBUG 모드

False면 was에서 static, media 안 돌아감(배포 모드)