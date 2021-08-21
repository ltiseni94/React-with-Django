# React-based components in Django App - Setup guide

### Credits to [Brad Traversy](https://github.com/bradtraversy "brad traversy github page") for inspiring this work with his video series: [Full Stack React & Django](https://www.youtube.com/watch?v=GieYIzvdt2U&t=1425s)

# Short guide - clone the repo and install the needed stuff.

This is a step-by-step guide on how to clone this repo and start using the React powered frontend app into your django project!

First, clone the repo:

    $ git clone https://github.com/ltiseni94/React-with-Django.git <anywhere-you-like>

Then, create a virtual environment:

    $ python3 -m venv venv

Activate it:

    $ source venv/bin/activate

And install the requirements:

    (venv) $ pip install -r requirements.txt

Now setup the npm packages:

    $ npm install

Finally, create the Django secret key:

    $ echo "SECRET_KEY = '<your-secret-key>'" > ./reactdjangosetup/secrets.py

Have a look at [this](https://djecrety.ir/ "django secret key generator") if you don't know how to generate a key.

Run the migration:

    (venv) $ python manage.py migrate

Now you should configure Live Sass Compiler. Have a look to section 3.3 of the __Long Guide__.

When Live Sass Compiler is configured, you can compile the sass code.

Don't forget to compile the react code, too:

    $ npm run dev

Finally, you should be able to see your hello world website!

    (venv) $ python manage.py runserver

# Long guide - how to generate this repo from scratch.

This is a step-by-step guide on how to integrate React.js-based frontend into one of your Django applications.

By following this guide, you will be able to:

- Include React-based components into your Django templates.

- Use Bootstrap 5 into your templates and React components.

- Customize the standard Bootstrap style using Sass and the Live Sass Compiler vsCode extension.

Prerequisites:

- This guide has been tested in a bash console in Ubuntu 20.04. It should work fine also for other Linux distributions. You probably can install anything in Windows, too, but you'll have to use the corresponding cmd / powershell commands that are not reported here.

- You should have already installed:
    - python 3.6+
    - npm
    - Visual Studio Code with the Live Sass Compiler extension (alternatively, you can use a sass compiler and compile scss files from bash).

Let's Start!

## 1 - Setup the Django Project

### 1.1 - Create the project directory

-   First of all, let's create our project directory anywhere we like:

        $ mkdir -p <path-to-project>/<project-folder-name>/

-   Navigate into your project folder

        $ cd <path-to-project>/<project-folder-name>/
    
### 1.2 - Create a virtual environment

You could install django globally, but you would regret this choice in a very short time. Use a virtual environment!

-   Into the project folder, type:

        $ python -m venv venv

-   or, if you want to use a specific python version (for example 3.9)

        $ python3.9 -m venv venv

-   If you want to check what are your available python versions, use the autocompletion hint. Press tab after having typed:

        $ python
    
    It will show you the available python versions installed.

### 1.3 - Install Django

-   Activate your virtual environment. In your project folder, type:

        $ source venv/bin/activate

-   _Optional_: Upgrade pip in your venv

        $ pip install --upgrade pip

-   Then, install Django using pip:

        $ pip install django

    This will make the django-admin command available, which we will need during next step.

### 1.4 - Create the Django Project

-   Wherever you like the most, create your Django project:

        $ django-admin startproject <project-name>
    
    At this point, you should have this file structure:

        |--- <project-folder-name>/
            |--- venv/
            |--- <project-name>/
                |--- <project-name>/
                    |--- __init__.py
                    |--- asgi.py
                    |--- settings.py
                    |--- urls.py
                    |--- wsgi.py
                |--- manage.py
        
-   Change the file structure to make it simpler. Be aware that project-folder-name and project-name must be different in order for this step to work. Move the django project files above using these commands: 

        $ mv <project-name>/ ./dummy
        $ mv dummy/* ./
        $ rm -r dummy/

    Now your file structure should look like this:

        |--- <project-folder-name>/
            |--- <project-name>/
                |--- __init__.py
                |--- asgi.py
                |--- settings.py
                |--- urls.py
                |--- wsgi.py
            |--- venv/
            |--- manage.py

## 2 - Setup the __frontend__ Django app

### 2.1 - Create the __frontend__ app

Now that you have a brand new Django project available, it is time to create the __frontend__ app.

Open a terminal, activate the virtual environment and navigate into the folder that contains the manage.py script. Then:

-   Create the Django app:

        (venv) $ python manage.py startapp frontend

-   Create the file structure for the frontend app:

        (venv) $ mkdir -p ./frontend/src/components
        (venv) $ mkdir -p ./frontend/{static,templates}

### 2.2 - Configure and Start the React App!

We will use npm, webpack and babel to configure React.

-   Start npm:

        $ npm init -y

    You should now see that a 'package.json' file has just been created.

-   Install webpack and babel stuff as dev dependencies:

        $ npm i -D webpack webpack-cli
        $ npm i -D @babel/core babel-loader @babel/preset-env @babel/preset-react babel-plugin-transform-class-properties

    You should now see that a 'package-lock.json', containing information on the packages installed, has been created.
    Also, the node_modules/ folder has been created, containing the packages source files.

-   Install react:

        $ npm i react react-dom prop-types

-   Create the configuration files:

        $ touch .babelrc
        $ touch webpack.config.js

-   Into _.babelrc_ type:

        {
            "presets": ["@babel/preset-env", "@babel/preset-react"],
            "plugins": ["transform-class-properties"]
        }

-   Into _webpack.config.js_ type:

        module.exports = {
            module: {
                rules: [
                    {
                        test: /\.js$/,
                        exclude: /node_modules/,
                        use: {
                            loader: "babel-loader"
                        }
                    }
                ]
            }
        }

-   Now, let's create the needed JavaScript files for a minimal React Application:

        $ touch ./frontend/src/index.js
        $ touch ./frontend/src/App.js
    
    Into your App.js write this code:

        import React from "react";

        function App() {
            return (
                <div className="container">
                    <h1>Hello World!</h1>
                </div>
            )
        }

        export default App;
    
    Into your index.js write this code:

        import React from 'react';
        import ReactDOM from 'react-dom';
        import App from './App';

        ReactDOM.render(
            <React.StrictMode>
                <App />
            </React.StrictMode>, 
            document.getElementById('app')
        );

-   Finally, let's adjust the scripts field into your package.json:

        "scripts": {
          "dev": "webpack --mode development --watch ./frontend/src/index.js -o ./frontend/static/",
          "build": "webpack --mode production ./frontend/src/index.js -o ./frontend/static/"
        }

    This will allow you to compile react app by using:

    Development (compiler remains active and automatically detect changes):
        
        $ npm run dev
    
    Production:    
        
        $ npm run build

    You should see that your React code has been compiled into the __main.js__ file inside the _static_ folder of the frontend app.

## 2.3 - Include the react App into your template!

It's time to see our Hello World page!

-   Create the template

    Create index.html file in the templates folder of your frontend django app:

        $ touch ./frontend/templates/index.html

    Copy and paste this code inside the template:

        <!DOCTYPE html>
        {% load static %}
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>React + Django: Supercool!</title>
        </head>
        <body>
            <div id='app'></div>
            <script src='{% static "main.js" %}'></script>
        </body>
        </html>
    
    Which is basically standard HTML5 boilerplate plus three important lines:

        {% load static %}
    
    that allows you to load files from the static/ folder in the frontend app

        <body>
            <div id='app'></div>
            <script src='{% static "main.js" %}'></script>
        </body>
    
    As you can see, we create a div with the 'app' id, in which our React App component will live. After that we add the compiled script main.js, that manipulates this div and populates it with the React App.

-   Install the frontend app into the Django project:

    Into \<project-name>/settings.py, you should add the frontend app in the list of installed apps:

        INSTALLED_APPS = [
            'django.contrib.admin',
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.messages',
            'django.contrib.staticfiles',
            'frontend',
        ]

-   Create the view for your frontend app:

    Inside ./frontend/views.py, type:

        from django.shortcuts import render

        # Create your views here.
        def index(request):
            return render(request, 'index.html')

-   Create the url configuration for the frontend app:

    Create the urls file:

        $ touch ./frontend/urls.py

    Inside it, type:

        from django.urls import path
        from .views import index

        urlpatterns = [
            path('', index)
        ]

-   Add the url configuration to the Django project:

    Inside \<project-name>/urls.py, your code should be:

        from django.contrib import admin
        from django.urls import path, include

        urlpatterns = [
            path('admin/', admin.site.urls),
            path('', include('frontend.urls')),
        ]

    Basically, you need to import the include function from django.urls and then you must add your frontend.urls to the urlpatterns list.

-   Let's try!

        (venv) $ python manage.py runserver

    Open http://127.0.0.1:8000/
    
    If everything went well, you should see "Hello World!" in your browser tab.

## 3 - Bootstrap 5

### 3.1 - Install Bootstrap 5 to your project

-   You can easily install Bootstrap 5 source using npm:

        $ npm i bootstrap

    You should now see that bootstrap is in your node_modules.

### 3.2 - Create the setup to include and customize Bootstrap 5 Sass

-   Create your main Sass file:

        $ touch ./frontend/src/main.scss

-   Inside the main.scss, paste this code:

        // Include any default variable overrides here (though functions won't be available

        @import "../../node_modules/bootstrap/scss/bootstrap";

        // Additional custom code goes here

    Basically, you can override the standard bootstrap theme by overriding variables (e.g. colors and other stuff) above the import statement.

    Under the import you can write your custom _scss_ code to create your own classes, media queries, etc.

### 3.3 - Install and configure Live Sass Compiler

Now it is time to compile our Sass code into regular css. We will use the Live Sass Compiler extension for Visual Studio Code. Otherwise you can just install your sass compiler and compile the scss file from command line.

-   Search for Live Sass Compiler in the Visual Studio Code Extensions and install it.

-   Now you should edit the vscode settings.json file, which is normally under this path:

        /home/<your-username>/.config/Code/User/settings.json

    You can also find it if you try to change Live Sass Compiler settings from Visual Studio Code. You should see the link _[edit in settings.json]()_.

    Into the settings.json, you should add these fields to the main json:

        "liveSassCompile.settings.autoprefix": [
            "> 1%",
            "last 3 versions"
        ],
        "liveSassCompile.settings.excludeList": [
            "**/node_modules/**",
            ".vscode/**"
        ],
        "liveSassCompile.settings.formats": [
            {
                "format": "expanded",
                "extensionName": ".css",
                "savePath": "/frontend/static/"
            },
            {
                "format": "compressed",
                "extensionName": ".min.css",
                "savePath": "/frontend/static/"
            }
        ],
        "liveSassCompile.settings.generateMap": false

    You may want to change the savePath fields if you did not use the same folder names. 
    
    Also, you may want to generate only the compressed and minified css. In that case, cancel the expanded format json.

-   Try to see if it works.

    First you should open the project folder in vsCode. Navigate into the project folder in a terminal session and type:

        $ code .

    Then push the __Watch Sass__ button on the bottom right of your Visual Studio Code UI and check the static folder of the frontend app. You should see the brand new _main.css_ and the _main.min.css_ files. Check also the Output tab to see what files were compiled and where are the output files.

### 3.4 - Include customized Bootstrap style in your template.

Last step is including a link to the stylesheet and include the JavaScript code needed by Bootstrap.

Your final index.html file should look like this:

    <!DOCTYPE html>
    {% load static %}
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet" href="{% static "main.min.css" %}">
        <title>React + Django: Supercool!</title>
    </head>
    <body>
        <div id='app'></div>
        <script src='{% static "main.js" %}'></script>
        <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js" integrity="sha384-IQsoLXl5PILFhosVNubq5LC7Qb9DXgDA9i+tQ8Zj3iwWAwPtgFTxbJ8NT4GN1R8p" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.min.js" integrity="sha384-cVKIPhGWiC2Al4u+LWgxfKTRIcfu0JTxR+EQDz/bgldoEyl4H0zUF0QKbrJ0EcQF" crossorigin="anonymous"></script>
    </body>
    </html>

Notice that:

-   We linked the main.min.css file:

        <link rel="stylesheet" href="{% static "main.min.css" %}">

-   We added Bootstrap cdn JavaScript files:

        <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js" integrity="sha384-IQsoLXl5PILFhosVNubq5LC7Qb9DXgDA9i+tQ8Zj3iwWAwPtgFTxbJ8NT4GN1R8p" crossorigin="anonymous"></script>
        
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.min.js" integrity="sha384-cVKIPhGWiC2Al4u+LWgxfKTRIcfu0JTxR+EQDz/bgldoEyl4H0zUF0QKbrJ0EcQF" crossorigin="anonymous"></script>

    This tutorial may get old, so check [here](https://getbootstrap.com/docs/5.0/getting-started/download/) for the latest versions of these two scripts.

Now run the Django server and you should see that the font is different and that the "Hello World!" is inside a container, so it is closer to the center.

## 4 - Let's make all this cool stuff a cool git repo (Optional)

### 4.1 - Create the .gitignore file

    $ touch .gitignore

Inside the .gitignore file type [this code](https://www.toptal.com/developers/gitignore/api/django)

On top of that, add these lines:

    node_modules/
    .vscode/
    frontend/static/
    **/secrets.py
    
Finally, create the secrets.py file in the same directory with the settings.py file. Move your SECRET_KEY inside the secrets.py to avoid that the SECRET_KEY is committed and pushed online. To generate secure SECRET_KEYs, have a look [here](https://djecrety.ir/)

-   _Optional step: run this command into a terminal session to create a requirements.txt file that can be useful to restore your venv when cloning this repo!_

        (venv) $ pip freeze > requirements.txt

Now you are ready to init the git repo, add files, commit and push to a remote. Create your repository in GitHub and:

    $ git init
    $ git add .
    $ git commit -m "first commit"
    $ git branch -M main
    $ git remote add origin <your-github-repo-url>.git
    $ git push -u origin main
