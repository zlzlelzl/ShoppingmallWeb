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
sudo apt-get upgrade
sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
sudo apt install certbot python3-certbot-nginx
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
virtualenv environment_3_9_10
source bin/bash/activate
```
pip install psycopg2-binary