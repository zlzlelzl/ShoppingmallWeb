default in bash
- pip install django
- pip install django-rest-framework
- pip install django-cors-headers
- pip install djoser
- pip install pillow
- INSTALLED_APPS.append("rest_framework","rest_framework.authtoken","corsheaders","djoser"]) in settings.py
- CORS_ALLOWED_ORIGINS = ["http://localhost:8080"] in settings.py (cors 에러 조심)
- MIDDLEWARE.append("corsheaders.middleware.CorsMiddleware") in settings.py
- urlpatterns.append(path("api/v1/", include("djoser.urls")), path("api/v1/", include("djoser.urls.authtoken"))) in urls.py
- python manage.py makemigrations
- python manage.py migrate (테이블 자동 생성)
- python manage.py createsuperuser
- python manage.py runserver (테스트)
- npm install -g @vue/cli
- vue create shoppingmall_vue
    - Manually select features
    - Babel, Router, Vuex, CSS Pre-processors
    - 3.x
    - Yes
    - Sass/SCSS (with dart-sass)
    - No
- npm install axios
- npm install bulma (css)
- npm run serve (start server)

- Vue는 다시 배우기

- python manage.py startapp product
- add 
```python
class Category(models.Model):
    name = models.CharField(max_length=255)
    slug = models.SlugField()

    class Meta:
        ordering = ("name",)

    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return f"/{self.slug}"

```
in models.py
- INSTALLED_APPS.append("product"]) in settings.py
- python manage.py makemigrations
- python manage.py migrate
- add
```python
from io import BytesIO
from PIL import Image

from django.core.files import File
from django.db import models


class Category(models.Model):
    name = models.CharField(max_length=255)
    slug = models.SlugField()

    class Meta:
        ordering = ("name",)

    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return f"/{self.slug}"


class Product(models.Model):

    category = models.ForeignKey(
        Category, related_name="products", on_delete=models.CASCADE)
    name = models.CharField(max_length=255)
    slug = models.SlugField()
    description = models.TextField(blank=True, null=True)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    image = models.ImageField(upload_to="uploads/", blank=True, null=True)
    thumbnail = models.ImageField(upload_to="uploads/", blank=True, null=True)
    date_added = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ("-date_added",)

    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return f"/{self.category.slug}/{self.slug}/"

    def get_image(self):
        if self.image:
            return "http://127.0.0.1:8000" + self.image.url
        return ""

    def get_thumbnail(self):
        if self.thumbnail:
            return "http://127.0.0.1:8000" + self.thumbnail.url
        else:
            if self.image:
                self.thumbnail = self.make_thumbnail(self.image)
                self.save()

                return "http://127.0.0.1:8000" + self.thumbnail.url
            else:
                return ""

    def make_thumbnail(self, image, size=(300, 200)):
        img = Image.open(image)
        img.convert("RGB")
        img.thumbnail(size)

        thumb_io = BytesIO()
        img.save(thumb_io, "JPEG", quality=85)

        thumbnail = File(thumb_io, name=image.name)

        return thumbnail

```
in models.py
- MEDIA_URL = "/media/" in settings.py
- MEDIA_ROOT = BASE_DIR / "media/" in settings.py
- add 
```python 
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
in urls.py

- add file and
```python
from django.db import models
from rest_framework import serializers

from .models import Category, Product


class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = (
            "id",
            "name",
            "get_absolute_url"
            "description",
            "price",
            "get_image",
            "get_thimbnail",
        )
```
in product.serializers.py


## ec2 setting

root required

``` bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx -y
sudo apt install certbot python3-certbot-nginx -y
sudo -u postgres psql
CREATE DATABASE djackets;
CREATE USER djacketsuser WITH PASSWORD 'djacketspassword';
ALTER ROLE djacketsuser SET client_encoding TO 'utf8';
ALTER ROLE djacketsuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE djacketsuser SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE djackets TO djacketsuser;
\q
sudo -H pip3 install --upgrade pip
sudo -H pip3 install virtualenv
mkdir -p /webapps/djackets
cd /webapps/djackets/
sudo groupadd --system webapps
sudo useradd --system --gid webapps --shell /bin/bash --home /webapps/djackets djackets
virtualenv environment_3_8_2
source environment_3_8_2/bin/activate
pip install psycopg2-binary
git clone ${my-django-project}
cp ShoppingmallWeb/Shoppingmall/ . -r
rm -r ShoppingmallWeb
chown -R djackets:webapps ../ (djackets folder)
cd Shoppingmall
pip install -r requirements.txt
vi secrets.json (this file gitignored)
python manageprod.py makemigrations
python manageprod.py migrate
pip install gunicorn
cd ..
# vi environment_3_8_2/bin/gunicorn_start
# sh gunicorn_start (test)
cp Shoppingmall/environment_settingfile/gunicorn_start environment_3_8_2/bin/
sudo chmod +x environment_3_8_2/bin/gunicorn_start
apt install supervisor
cp /webapps/djackets/Shoppingmall/environment_settingfile/djackets.conf /etc/supervisor/conf.d/
mkdir /webapps/djackets/environment_3_8_2/logs/
supervisorctl reread
supervisorctl update
supervisorctl status
cd /etc/nginx/sites-available/
vi zlzlelzl.tk.nginx.conf
cd ../sites-enabled
sudo certbot -d zlzlelzl.tk (y, a, 2)
ln -s ../sites-available/zlzlelzl.tk .
```



```bash
upstream djackets_app_server {
	server unix:/webapps/djackets/environment_3_8_2/run/gunicorn.sock fail_timeout=0;
}

server {
	listen 80;
	server_name zlzlelzl.tk;
	return 301 https://zlzlelzl.tk$request_uri;
}

server {
	listen 443 ssl;
	server_name zlzlelzl.tk;

	client_max_body_size 4G;

	access_log /webapps/djackets/environment_3_8_2/logs/nginx-django-access.log;
	error_log /webapps/djackets/environment_3_8_2/logs/nginx-django-error.log;

	ssl_certificate /etc/letsencrypt/live/zlzlelzl.tk/fullchain.pem;
	ssl_certificate_key /etc/letse ncrypt/live/zlzlelzl.tk/privkey.pem;

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_prefer_server_ciphers on;
	ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

	location /static/ {
		alias /webapps/djackets/Shoppingmall/static/;
	}

	location /media/ {
		alias /webapps/djackets/Shoppingmall/media/;
	}

	location / {
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

		proxy_set_header Host $http_host;

		proxy_redirect off;

		if (!-f $request_filename) {
			proxy_pass http://djackets_app_server;
		}
	}
}
```

certbot limited

## progress

- make superuser and login
- add categorys and products
- modify prod uris from /product.py.Products
- modify prod uri from /shoppingmall_vue/src/main.js.axios.defaults.baseURL and <code>npm run build</code>

```bash
# zip dist/ and transfer deploy server
# https://cloudlinuxtech.com/scp-permission-denied/
$ scp -i {keyFile}.pem {fileName} {username}@{host}:{path}
```