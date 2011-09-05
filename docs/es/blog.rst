*******************************
Mi Primera Aplicacion con Shake
*******************************

.. topic :: Requisitos
    
    Instalar **SQLAlchemy** ::

        $ pip install sqlalchemy


1. Crear una nueva app ::

        $ shake blog

   Este comando genera un esqueleto para la aplicacion ::
   
       ├── app
       │   ├── app.py
       │   ├── controllers.py
       │   ├── __init__.py
       │   ├── models.py
       │   ├── settings.py
       │   ├── urls.py
       │   └── views
       │       ├── base.html
       │       ├── error.html
       │       ├── index.html
       │       └── not_found.html
       ├── manage.py
       └── static
           ├── css
           │   └── blog.css
           ├── images
           │   └── favicon.ico
           ├── js
           │   ├── blog.js
           │   ├── jquery-1.6.2.min.js
           │   └── underscore-1.1.6.min.js
           └── robots.txt

2. Agregar shakext. shakext tiene que estar al mismo nivel que app

3. Creando un modelo

   Editar el archivo ``models.py`` ::

      from shakext.sqlalchemy import SQLAlchemy

      class Post(db.Model):
          id  = db.Column(db.Integer, primary_key=True)
          title = db.Column(db.String(255), nullable=False)
          body = db.Column(db.UnicodeText, nullable=False, default=u'')
          created = db.Column(db.DateTime, default=datetime.utcnow, nullable=False)

4. Escoger la base de datos 
  
  Descomentar en ``settings.py`` ::
        
      # settings.py

      SQLALCHEMY_URI = 'sqlite:///db.sqlite'

5. Sincronizar los modelos con la base de datos. ::

      $ python manage.py syncdb

6. Agregando Registros ::

    >>> from app.models import Post, db
    >>> p = Post(title=u"Hello World!", body=u"My first post")
    >>> db.session.add(p)
    >>> db.session.commit()

7. Creando el controlador

  Editar el archivo ``controllers.py`` ::

      # -*- coding: utf-8 -*-

      from shake import redirect, url_for, flash, NotFound

      # from shakext.auth import protected

      from . import settings
      from .models import db
      from .settings import render

      def index(request):
          return render('index.html', **locals())

8. Servir los posts ::

    # controllers.py

    def index(request):
        posts = Post.query.all()
        return render('index.html', **locals())

9. Mostrar los posts
   
   Editar ``views/index.html``

   .. code-block:: django

      {% extends "base.html" %}
        
      {% block title %}Hello{% endblock %}
      {% block content %}
      <ul>
        {% for post in posts %}
          <li>
            <h2>{{ post.title }}</h2>
            <p>{{ post.body }}</p>
            <p>{{ post.created }}</p>
          </li>
        {% endfor %}{# posts #}
      </ul>
      {% endblock %}{# content #}

10. Mostrar los post en la pagina de inicio
    Editar el archivo ``urls.py`` ::

        # -*- coding: utf-8 -*-

        from shake import Rule, EndpointPrefix, Submount

        urls = [
            EndpointPrefix('app.controllers.', [
                Rule('/', 'index'),
                ]),
            ]
