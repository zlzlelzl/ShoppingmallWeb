## nginx 팁

nginx 플로우를 정확하게 파악해야 트러블슈팅을 할 수 있음
proxy_pass 추가하여 L7 로드밸런싱
supervisor는 health check 기능이 있다.
api 도메인 따로 만들기

http -> /etc/nginx/nginx.conf -> /etc/nginx/sites-enabled/* -> servers(api, front)

servers(front) -> 

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