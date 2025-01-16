Voici un **TODO technique** détaillé pour réaliser le TP sur le développement d'une API RESTful avec Django :

---

### **TODO Technique pour TP Web Service REST Django**

#### **1. Préparation de l'environnement**
- [ ] Installer Python 3.8+ si non disponible.
- [ ] Créer un environnement virtuel :
  ```bash
  python -m venv env
  ```
- [ ] Activer l'environnement virtuel :
  - **Windows** :
    ```bash
    env\Scripts\activate
    ```
  - **Linux/Mac** :
    ```bash
    source env/bin/activate
    ```
- [ ] Installer Django et Django REST Framework :
  ```bash
  pip install django djangorestframework
  ```

#### **2. Création du projet Django**
- [ ] Générer un projet Django :
  ```bash
  django-admin startproject gamesapi
  ```
- [ ] Naviguer dans le répertoire du projet :
  ```bash
  cd gamesapi
  ```

#### **3. Création de l’application**
- [ ] Créer une application `games` :
  ```bash
  python manage.py startapp games
  ```
- [ ] Ajouter `games` et `rest_framework` dans `INSTALLED_APPS` dans `gamesapi/settings.py`.

#### **4. Création du modèle**
- [ ] Définir le modèle `Game` dans `games/models.py` :
  ```python
  from django.db import models

  class Game(models.Model):
      name = models.CharField(max_length=200, blank=False)
      release_date = models.DateTimeField()
      category = models.CharField(max_length=100, blank=True)
      played = models.BooleanField(default=False)

      def __str__(self):
          return self.name
  ```
- [ ] Appliquer les migrations :
  ```bash
  python manage.py makemigrations games
  python manage.py migrate
  ```

#### **5. Création des serializers**
- [ ] Créer un fichier `games/serializers.py` :
  ```python
  from rest_framework import serializers
  from .models import Game

  class GameSerializer(serializers.ModelSerializer):
      class Meta:
          model = Game
          fields = '__all__'
  ```

#### **6. Création des vues**
- [ ] Implémenter les vues dans `games/views.py` :
  ```python
  from rest_framework import generics
  from .models import Game
  from .serializers import GameSerializer

  class GameListCreateView(generics.ListCreateAPIView):
      queryset = Game.objects.all()
      serializer_class = GameSerializer

  class GameDetailView(generics.RetrieveUpdateDestroyAPIView):
      queryset = Game.objects.all()
      serializer_class = GameSerializer
  ```

#### **7. Configuration des URLs**
- [ ] Créer un fichier `games/urls.py` et y définir les routes :
  ```python
  from django.urls import path
  from .views import GameListCreateView, GameDetailView

  urlpatterns = [
      path('games/', GameListCreateView.as_view(), name='game-list-create'),
      path('games/<int:pk>/', GameDetailView.as_view(), name='game-detail'),
  ]
  ```
- [ ] Inclure les URLs de l'application dans `gamesapi/urls.py` :
  ```python
  from django.contrib import admin
  from django.urls import path, include

  urlpatterns = [
      path('admin/', admin.site.urls),
      path('api/', include('games.urls')),
  ]
  ```

#### **8. Tests de l’API**
- [ ] Lancer le serveur Django :
  ```bash
  python manage.py runserver
  ```
- [ ] Tester les endpoints avec Postman ou CURL :
  - **POST** : Créer un jeu (`http://127.0.0.1:8000/api/games/`).
  - **GET** : Récupérer tous les jeux (`http://127.0.0.1:8000/api/games/`).
  - **GET** : Récupérer un jeu par ID (`http://127.0.0.1:8000/api/games/1/`).
  - **PUT/PATCH** : Mettre à jour un jeu (`http://127.0.0.1:8000/api/games/1/`).
  - **DELETE** : Supprimer un jeu (`http://127.0.0.1:8000/api/games/1/`).

#### **9. Validation des données**
- [ ] Ajouter des validations spécifiques dans le serializer, si nécessaire :
  ```python
  def validate_name(self, value):
      if len(value) < 3:
          raise serializers.ValidationError("Le nom doit contenir au moins 3 caractères.")
      return value
  ```

#### **10. Documentation (optionnel)**
- [ ] Ajouter Swagger pour documenter l'API :
  ```bash
  pip install drf-yasg
  ```
- [ ] Configurer Swagger dans `gamesapi/urls.py` :
  ```python
  from rest_framework import permissions
  from drf_yasg.views import get_schema_view
  from drf_yasg import openapi

  schema_view = get_schema_view(
      openapi.Info(
          title="Games API",
          default_version='v1',
          description="Documentation de l'API pour les jeux",
      ),
      public=True,
      permission_classes=(permissions.AllowAny,),
  )

  urlpatterns += [
      path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
  ]
  ```

---

### Livrables :
1. Modèle de données `Game`.
2. Sérialiseur pour `Game`.
3. Endpoints fonctionnels pour les opérations CRUD.
4. Documentation Swagger si ajoutée.

Bon courage pour votre TP ! 🚀