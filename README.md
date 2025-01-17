# homedataflask App

Flask App, Deployable to Heroku for Analysis of data repo stored at github.com/linknlearn/homedata

Endpoint can be found here:

https://homedataflask.herokuapp.com/

## Using Heroku

Heroku is a cloud Platform as a Service (PaaS) which allows developers to build, run and scale applications.  Heroku has the following features:

* Sets up domain names for each individual app, for example - exampleapp.heroku.com
* Keeps all applications within its own container, known as a "Dyno"
* Uses git as a way to interact with it - allows permitted users to push files to run an app

### Deploying to Heroku

* Applications are sent to Heroku using either Git, Github, Dropbox or via an API.
* "Build-packs" are a list of dependencies that include the application as well as all of its dependencies and installed packages.
* "Slugs" are a combination of the source code that we, the developers write, dependencies the language runtime itself (e.g. python) and the compiled and installed build system which is ready for execution.
* Heroku also allows configuration variables to be stored within the Dyno for security purposes (as opposed to keeping it in the source code).
* There are also a variety of add-ons from third parties which allow various databases and other features to be bundled along with one's own app, for example, Postgres add-ons that allow usage of Postgres databases.
* A "Release" is a combination of a slug, config variables and add-ons.

You can log into Heroku by using the Heroku CLI.  Documentation for the Heroku CLI is here:

https://devcenter.heroku.com/articles/heroku-cli-commands

#### Adding Requirements

Requirements are basically the dependencies which will go into the build-packs to allow your Heroku App to work.  In the case of python, these are all of the libraries needed, and will be included in a .txt file.

In order to quickly grab all of the requirements needed to deploy, you can run the following command:

pip freeze > requirements.txt

This grabs every single library currently installed in your computer and throws them into the requirements.txt file.  This should be used in the case that your app is very complicated.  However if you are sure that you know what the requirements are, you can simply add them in manually to requirements.txt.

For example, for the Flask requirement you would add:

Flask==1.0.2

#### Installing Gunicorn

Gunicorn 'Green Unicorn' is a Python WSGI HTTP Server for UNIX.  You have to install this in order to serve the Flask app, because Flask doesn't come with its own server capability.

$ pip install gunicorn

Information about Gunicorn can be found at https://gunicorn.org/

After installing Gunicorn, ensure that your requirements file is updated with:

pip freeze > requirements.txt

##### Making Viewable Within Atom Text Editor

Incidentally, if you are using the popular Atom text editor, .txt files are defined as "VCS Ignored Files" - default files that will be ignored by the system as they would in .gitignore within github.  In order to show these files, you have to go under Preferences > Settings and then deselect to ignore these types of files in a couple different places:

1. Within the main Core Settings - just untick the box for ignore VCS Files.
2. Within the package, "Tree View" - go under settings and untick the box.

Once you have done this, you should be able to view the .txt file within Atom.

#### Create a Virtual Environment To Test Gunicorn

Set up a virtual environment in order to isolate our Flask application from the other Python files on the system.

$ pip install virtualenv

We are using the parent directory "homedataflask"  We navigate into this parent directory and use virtualenv to create a virtual project environment as follows:

$ virtualenv --no-site-packages helloprojenv

When this command is executed you should see the following:

Using base prefix '/Users/X/anaconda3'
New python executable in /Users/X/Documents/Projects/homedataflask/helloprojenv/bin/python
Installing setuptools, pip, wheel...
done.

So that new project environment is "helloprojenv"

Once you have done this, you should see the following files within your project environment:

bin/  include/ lib/

##### Other Notes

Running virtualenv with the option --no-site-packages will not include the packages that are installed globally. This can be useful for keeping the package list clean in case it needs to be accessed later. [This is the default behavior for virtualenv 1.7 and later.]

In order to keep your environment consistent, it’s a good idea to “freeze” the current state of the environment packages. To do this, run:

$ pip freeze > requirements.txt

This will be a way of creating a requirements.txt file with a reduced number of dependencies listed.

##### Activate the Project Environment

$ source helloprojenv/bin/activate --no-site-packages

Your prompt will change to indicate that you are now operating within the virtual environment.

###### Adding New Installations to Project Environment with --no-site-packages Option

Let's take a quick look at what happens in the requirements.txt file if we create it.  If you run the following:

$ pip freeze > requirements.txt

You will see that, running in the virtual environment, nothing will happen, nothing will be added to requirements.txt.

Install flask with:

$ pip3 install flask

Then once again run:

$ pip freeze > requirements.txt

Then, look in the requirements.txt file, and you will see the following will have been added:

Click==7.0
Flask==1.0.2
itsdangerous==1.1.0
Jinja2==2.10
MarkupSafe==1.1.0
Werkzeug==0.14.1

This is far fewer requirements than we would have had if we would have just started with the requirements file from our local machine, which had tons of different python libraries installed from years of different work.  This will be necessary to bring down the Slug building time to the point where it will be possible to build the app.  If we try to build with too many packages, then we will get a time out and it won't serve.

Obviously, you are going to have to install all of the necessary packages within the virtual environment needed to run the application as well as run the server.  Once you're done installing all of the needed applications in the virtual environment, you will have to run the following to finish things off:

$ pip freeze > requirements.txt

#### Running the App Locally with Gunicorn

Install gunicorn-flask into the virtual environment with:

$ pip install gunicorn flask

#### Looking At the App Itself

The app that we have built at hello.py has the following points:

* We import flask and instantiate a Flask object.
* We use this to define functions that should be run when a specific route is requested.
* We call our Flask application "app" to replicate the examples we find in the WSGI specification.

So when we run our main app, "hello.py" we are able to actually serve the Flask instance through, "lazy loading," and it will display the app on a web page at 0.0.0.0:5000 while displaying the following in the shell:

$ python3 hello.py
 * Serving Flask app "hello" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 148-290-852

##### Creating the WSGI Entry Point

WSGI or, "Web Server Gateway Interface" is a calling convention for web servers to forward requests to web applications or frameworks written in Python.

The first thing we need to create is a WSGI file which imports the Flask instance from our application and runs it.

Our WSGI code is as follows:

`
from hello import app

if __name__ == "__main__":
    app.run()
`

Within our project structure, we have the following folders:

-homedataflask
--helloprojevn

We should put the file wsgi.py in --helloprojenv not -homedataflask because we have not programmed the ability for the wsgi.py to look into a separate folder structure for the hello.py app.

##### Testing Gunicorn's Ability to Serve the Project

While in the virtual environment, you can test guinicorn by running:

gunicorn --bind 0.0.0.0:8000 wsgi

Note: we had to change all instances of the word, "app" to "application" in order to work with wsgi convention.

Once this is running, you should see the app serve and find the following on your shell:

gunicorn --bind 0.0.0.0:8000 wsgi
[2018-12-24 14:01:33 -0600] [65101] [INFO] Starting gunicorn 19.9.0
[2018-12-24 14:01:33 -0600] [65101] [INFO] Listening at: http://0.0.0.0:8000 (65101)
[2018-12-24 14:01:33 -0600] [65101] [INFO] Using worker: sync
[2018-12-24 14:01:33 -0600] [65104] [INFO] Booting worker with pid: 65104
[2018-12-24 14:02:19 -0600] [65101] [CRITICAL] WORKER TIMEOUT (pid:65104)
[2018-12-24 14:02:19 -0600] [65104] [INFO] Worker exiting (pid: 65104)
[2018-12-24 14:02:20 -0600] [65133] [INFO] Booting worker with pid: 65133

Once you have proved that Gunicorn can run the app, you can delete the environment folder by deleting the file you used to host the environment while saving your main app files from that folder.

Exit the virtual environment and run the following:

deactivate

Then, move the appropriate folders around and delete the virtual environment folders.

###### Picking Locations In the File for Gunicorn

Gunicorn takes a flag, --chdir, that lets you select which directory your Python app lives in. So, if you have a directory structure like:

my-project/
  Procfile
  my_folder/
    my_module.py
and my_module.py contains:

app = Flask(__name__, ...)

You can put the following in your Procfile:

web: gunicorn --chdir my_folder my_module:app

#### Adding a Procfile

Procfile is a mechanism for declaring what commands are run by your application's Dynos on the Heroku platform. It's basically a list of commands that Heroku reads in order to get things started.  Whatever is in the Procfile should be reflective of what we did in the above section with Gunicorn to get the app hosted on our local machine.

Create a new file with Procfile as the name and do not add any extension. Add this line below:

web: gunicorn wsgi.py

This above line refers to the Flask app which we have built.  This was stored in the hello.py file and called out as follows:

app = Flask(__name__)

That being said, if our app was called something like, "thisapp = Flask(__name__)" then we would need to use:

web: gunicorn app:thisapp

In our case, we're using:

web: gunicorn hello:application

#### Deploying to Heroku

At this point you have a lot of various dependencies that you may have added so it's probably a good time to run:

pip freeze > requirements.txt

We have already created a Dyno within the Heroku dashboard with endpoint:

https://homedataflask.herokuapp.com/

So now we need to log in with Heroku CLI and push our build-pack (dependencies) and source code to create a slug on the dyno.

"homedataflask" is the name of our application, which by the way - must be unique across Heroku.

https://git.heroku.com/homedataflask.git is the Heroku git remote repo where our application lives on Heroku.  We have to push our application to the master branch of the above git URL.

##### Managing Individual Heroku Apps

So assuming that you have the heroku cli installed and that you were able to authenticate to your heroku instance, we need to manage individual apps.

So to manage individual apps, from the command line we use commands that we find in the Heroku documentation, including the shortlist of CLI usage here:  https://devcenter.heroku.com/articles/using-the-cli

To look at the apps we have listed on our heroku account

$ heroku apps

We should get the following output:

=== "user_email@example.com" Apps
homedataflask

Doing this shows that you are able to connect to Heroku and that you are able to see the app that you are trying to build to.

##### Pushing Source Code to Heroku Git

Heroku links your projects based on the heroku git remote. To add your Heroku remote as a remote in your current repository, use the following command (all of this is found under the "Deploy" section on your Heroku dashboard):

$ heroku git:remote -a homedataflask
set git remote heroku to https://git.heroku.com/homedataflask.git
/homedataflask$ git add .
/homedataflask$ git commit -am "make it better"
[master 59c63bd] make it better
 1 file changed, 21 insertions(+)
/homedataflask$ git push heroku master

You should get the result:

Counting objects: 1940, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (1897/1897), done.
Writing objects:  21% (408/1940), 2.56 MiB | 86.00 KiB/s
Total 1940 (delta 541), reused 3 (delta 0)

###### Debugging For Production - Building Slug

* No default language could be detected for this app

This article explains buildpacks: https://devcenter.heroku.com/articles/buildpacks

$ heroku buildpacks:set heroku/python

You should get the following result:

Buildpack set. Next release on homedataflask will use heroku/python.
Run git push heroku master to create a new release using this buildpack.

* App not compatible with buildpack:

https://devcenter.heroku.com/articles/buildpacks#detection-failure

Basically this means the buildpack is not set to the right language, or there is no requirements file.

* No matching distribution found for XXXX

This means that Heroku can't work with the distribution called out.  You can delete these requirements from the requirement file one by one as a way to potentially fix this issue, of course you can't delete requirements that the app actually needs.  Note - it would have been better to make the requirements file within the virtual environment rather than just running on the local machine, as you may have had many packages installed from varieties of programs being built in the past, some of which could have been out of date.  Some distributions that we had errors thrown on were the following during our installation:

Couldn't Find Versions:

blaze==0.11.3 (appears to be out of date, last updated 2016)

Connected to Anaconda
conda==4.5.11
conda-build==3.15.1
clyent==1.2.2
navigator-updater==0.2.1

datashape==0.5.4
mkl-fft==1.0.4
mkl-random==1.0.1

odo==0.5.1

Only Windows and OSX Supported
xlwings==0.11.8

Only OSX Supported
appscript==1.0.1
appnope==0.1.0

####### May Need Further Debugging

Not Supported
gmpy2==2.0.8 - https://pypi.org/project/gmpy2/ Allows floating-point integers.
pyodbc==4.0.24

Error found:

In file included from src/gmpy2.c:426:0:
remote:            src/gmpy.h:252:12: fatal error: mpfr.h: No such file or directory
remote:             #  include "mpfr.h"

Note - we may need to install mpfr.h according to the Installation documentation.
https://gmpy2.readthedocs.io/en/latest/intro.html#installation


* NLTK 'nltk.txt' not found

remote: -----> Downloading NLTK corpora…
remote:  !     'nltk.txt' not found, not downloading any corpora
remote:  !     Learn more: https://devcenter.heroku.com/articles/python-nltk

* Warning: Your slug size exceeds our soft limit (493 MB) which may affect boot time.


###### Debugging After Build - Looking At Server Logs

After we deploy and build the Slug, the application may still not work!

heroku logs --tail

* 2018-12-25T15:13:27.030264+00:00 heroku[router]: at=error code=H20 desc="App boot timeout" method=GET path="/" host=homedataflask.herokuapp.com




###### A Word on Package Dependency Management

https://medium.com/python-pandemonium/better-python-dependency-and-package-management-b5d8ea29dff1

####### Docker Images

The reason why one would want to use a docker image is to able to 100% mimic a remote environment on one's local machine.  The reason why one would want to mimic a remote environment on one's local machine is to be able to understand which packages are going to work and to prevent the installation of incompatible packages which will slow down the deploy process.  For example, including "appscript" in the requirements.txt file lead to the dependency appscript being found, however since it is only compatible with the operating system OS, then upon attempting to install it within the Heroku build process, it was rejected, making the entire setup process fail.


# Cleaning Up The Slug / server

https://robots.thoughtbot.com/how-to-reduce-a-large-heroku-compiled-slug-size


# App Architecture

Having built the app, we obtained a better understanding of what the actual architecture looks like and so can draw a better sketch:

![App Architecture](https://github.com/LinkNLearn/homedataflask/blob/master/img/homedataflask_arch.png?raw=true)
