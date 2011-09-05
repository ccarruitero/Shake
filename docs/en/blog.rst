**********************************
Your first application using shake
**********************************

.. topic :: Requirements
    
    Install **SQLAlchemy** ::

        $ pip install sqlalchemy


1. Create a new app ::

        $ shake blog

  
   This command generates a skeleton for the application ::
   
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

2. Add shakext. shakext has to be located in the same level of your app

3. Create a model

   Edit ``models.py`` file ::

      from shakext.sqlalchemy import SQLAlchemy

      class Post(db.Model):
          id  = db.Column(db.Integer, primary_key=True)
          title = db.Column(db.String(255), nullable=False)
          body = db.Column(db.UnicodeText, nullable=False, default=u'')
          created = db.Column(db.DateTime, default=datetime.utcnow, nullable=False)

4. Choose your database 
  
   Uncomment in ``settings.py`` the following line ::
        
      # settings.py

      SQLALCHEMY_URI = 'sqlite:///db.sqlite'

5. Sync your model with the database. ::

      $ python manage.py syncdb

6. Add records ::

    >>> from app.models import Post, db
    >>> p = Post(title=u"Hello World!", body=u"My first post")
    >>> db.session.add(p)
    >>> db.session.commit()

7. Create the controller 

  Edit ``controllers.py`` file ::

      # -*- coding: utf-8 -*-

      from shake import redirect, url_for, flash, NotFound

      # from shakext.auth import protected

      from . import settings
      from .models import db
      from .settings import render

      def index(request):
          return render('index.html', **locals())

8. Serve the posts ::

    # controllers.py

    def index(request):
        posts = Post.query.all()
        return render('index.html', **locals())

9. Show the posts
   
   Edit ``views/index.html`` file

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

10. Show the posts in the home page 

    Edit ``urls.py`` file ::

        # -*- coding: utf-8 -*-

        from shake import Rule, EndpointPrefix, Submount

        urls = [
            EndpointPrefix('app.controllers.', [
                Rule('/', 'index'),
                ]),
            ]
