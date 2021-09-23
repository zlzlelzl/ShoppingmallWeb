# 장고 쇼핑몰 클론코딩

## progress

default in bash
- pip install django
- pip install django-rest-framework
- pip install django-cors-headers
- pip install djoser
- pip install pillow
- INSTALLED_APPS.append("rest_framework","rest_framework.authtoken","corsheaders","djoser"]) in settings.py
- CORS_ALLOWED_ORIGINS = ["http://localhost:8080"] in settings.py
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
- add \<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.2/css/all.min.css"> in shoppingmall_vue.public.index.html.header
- del \<style lang="scss"> in App.vue
- add @import "../node_modules/bulma"; in shoppingmall_vue.src.App.vue.\<style lang="scss">
- del \<template> in App.vue
- add
```html
<div id="wrapper">
    <nav class="navbar is-dark">
      <div class="navbar-brand">
        <router-link to="/" class="navbar-item"><strong>Djackets</strong></router-link>

        <a class="navbar-burger" aria-label="menu" aria-expanded="false" data-target="navbar-menu" @click="showMobileMenu = !showMobileMenu">
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>

      <div class="navbar-menu" id="navbar-menu" v-bind:class="{'is-active': showMobileMenu }">
        <div class="navbar-start">
          <div class="navbar-item">
            <form method="get" action="/search">
              <div class="field has-addons">
                <div class="control">
                  <input type="text" class="input" placeholder="What are you looking for?" name="query">
                </div>

                <div class="control">
                  <button class="button is-success">
                      <span class="icon">
                      <i class="fas fa-search"></i>
                      </span>
                  </button>
                </div>
              </div>
            </form>
          </div>
        </div>

        <div class="navbar-end">
          <router-link to="/summer" class="navbar-item">Summer</router-link>
          <router-link to="/winter" class="navbar-item">Winter</router-link>

          <div class="navbar-item">
            <div class="buttons">
              <template v-if="$store.state.isAuthenticated">
                <router-link to="/my-account" class="button is-light">My account</router-link>
              </template>

              <template v-else>
                <router-link to="/log-in" class="button is-light">Log in</router-link>
              </template>

              <router-link to="/cart" class="button is-success">
                <span class="icon"><i class="fas fa-shopping-cart"></i></span>
                <span>Cart ({{ cartTotalLength }})</span>
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <div class="is-loading-bar has-text-centered" v-bind:class="{'is-loading': $store.state.isLoading }">
      <div class="lds-dual-ring"></div>
    </div>

    <section class="section">
      <router-view/>
    </section>

    <footer class="footer">
      <p class="has-text-centered">Copyright (c) 2021</p>
    </footer>
  </div>
```
in App.vue.\<template>