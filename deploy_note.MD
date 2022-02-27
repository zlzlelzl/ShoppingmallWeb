## nginx 팁

nginx 플로우를 정확하게 파악해야 트러블슈팅을 할 수 있음
upstream에 server 추가하여 로드밸런싱
supervisor는 health check 기능이 있다.

http -> /etc/nginx/nginx.conf -> /etc/nginx/sites-enabled/* (포트 겹치면 오류) -> server(80,443) -> server(443) -> static, media, proxy_pass {function_name}:upstream -> server unix:{SOCKFILE:gunicorn}.sock (SOCKFILE == /gunicorn {djangoFolder}.wsgi:application) -> supervisor

supervisor -> /etc/supervisor/conf.d/{configFileName}.conf -> [program:{filename}] -> gunicorn -> SOCKFILE:gunicorn

### certbot fullkeychain 오류

unlink /etc/nginx/sites-enabled/{file-name}

### service nginx restart " " in ~ 오류

들여쓰기는 탭만 인식함

### 포트 사용중일때

sudo apt install net-tools(for netstat)
fuser -k -n tcp 80 (kill some port)