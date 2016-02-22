# Table of Contents
- [Deploy Databases](#deploy-databases)
	- [Deploy Neo4j to graphenedb Platform](#deploy-neo4j-to-graphenedb-platform)
		- [Prepare Data](#prepare-data-of-neo4j)
		- [Import the Compress File to graphededb](#import-the-compress-file-to-graphededb)
	- [Deploy Mongodb to mongolab](#deploy-mongodb-to-mongolab)
		- [Prepare Data](#prepare-an-available-mongolab-account)
		- [Import Mongo Data to mongolab](#import-mongo-data-to-mongolab)
- [Deploy Source Code to Heroku](#deploy-to-heroku)
	- [Set Up](#set-up)
	- [Prepare the App](#prepare-the-app)
		- [Case1: Clone Heroku Repo](#clone-heroku-repo)
		- [Case2: Use Local Repo](#use-local-repo)
	- [Deploy the App](#deploy-the-app)
	- [View Logs](#view-logs)
- [Example: Brandgraph Deploy](#example)
	- [Deploy Neo4j to graphenedb](#deploy-brandgraph-database-neo4j)
		- [Prepare Compressed File](#prepare-compress-file-of-brandgraph-database)
		- [Import to graphededb](#import-the-compress-file-to-graphededb)
	- [Deploy API to Heroku](#deploy-brandgraph-api-to-heroku)
	- [Deploy Web to Heroku](#deploy-brandgraph-web-to-heroku)
- [Reference](#reference)

# <a name="deploy-databases">Deploy Databases</a>

## <a name="deploy-neo4j-to-graphenedb-platform">Deploy Neo4j to graphenedb</a>

### <a name="prepare-data-of-neo4j">Prepare Data</a>

- To start neo4j, issue the following command in a system shell and make sure the site http://localhost:7474/browser/ to visit normal:

	```
	$ neo4j console
	```
- To compress database file data.db, issue the following command in a system shell: 

	```
	$ tar -cjf data.tar.bz2 %YOUR_NEO4J_DB_PATH%/neo4j-community/data/data.db
	```
### <a name="import-the-compress-file-to-graphededb">Import the Compress File to graphededb</a>

- Register a [graphededb](http://app.graphenedb.com/) account
- Click the `Create database` button
- Check to the `Admin` tab and click `Restore database` button, then click `upload file`, choose the compress file `data.tar.bz`, and click `Restore` waiting for progress 100%
-  Check to 'Connection' Tab and you will see the REST URL, REST USERNAME and REST PASSWORD

## <a name="deploy-mongodb-to-mongolab">Deploy Mongodb to mongolab</a>

### <a name="prepare-an-available-mongolab-account">Prepare Data</a>

- Register a [mongolab](https://mongolab.com/) account and login in
- Click `Create new` button
- Choose a free cloud provider, fill in the `database name` in the form and cilck the `create new MongoDB deployment` button
- Check to the `Users` tab and click the `Add database user` button, fill in your usename and password

### <a name="import-mongo-data-to-mongolab">Import Mongo Data to mongolab</a>

In [mongolab](https://mongolab.com/), switch to the `Tools` tab and find which data type you want import, you will see the command follow:


```
Prepare a available json file
Import collection
% mongoimport -h <Your own mongolab REST URL> -d <databasename> -c <collection> -u <user> -p <password> --file <input file>

Export collection
% mongoexport -h <Your own mongolab REST URL> -d <databasename> -c <collection> -u <user> -p <password> -o <output file>
```

Take it as a reference and replace `<>` parts with right value to import mongo data correctly.

# <a name="deploy-to-heroku">Deploy Source Code to Heroku</a>

## <a name="set-up">Set Up</a>

Assumes that you have a free [Heroku account](https://signup.heroku.com/dc)
and have ran the application locally successfully. 


In the next step you will install the Heroku Toolbelt, this provides you access to the Heroku Command Line Interface (CLI), which can be used for managing and scaling your applications and add-ons. Once installed, you can use the heroku command from your command shell.

Log in using the email address and password you used when creating your Heroku account:
	
	$  heroku login
	Enter your Heroku credentials.
	Email: python@example.com
	Password:
	...
	
Authenticating is required to allow both the heroku and git commands to operate.


## <a name="prepare-the-app">Prepare the App</a>

In this step, you will prepare a simple application that can be deployed. There are two ways to structure the application, we would introduce them separately: 


### <a name="clone-heroku-repo">Case1: Clone Heroku Repo</a>

Execute the following commands to clone the sample application：	
	
	$ git clone <HEROKU-DEMO-REPO-PATH>
	$ cd python-getting-started
	
You now have a functioning git repository that contains a simple application as well as a requirements.txt file. 

If you wanna to deploy a python application, <HEROKU-DEMO-REPO-PATH> should be replaced with the demo application path,for example:[https://github.com/heroku/python-getting-started.git](https://github.com/heroku/python-getting-started.git), for all optional applications [please visit here](https://devcenter.heroku.com/start).

### <a name="use-local-repo">Case2: Use Local Repo</a>

Assume you already have a repo in local and it can be ran successfully, 
you may have to define your Procfile. Heroku would find the Procfile in application's root directory as the boot script.  

**Example: Python Application Configuration: **

*<Application-DIRECTORY>/Procfile*

```
web: gunicorn run:app --log-file -
```

*<Application-DIRECTORY>/run.py*

```
from app import app
```


*<Application-DIRECTORY>/app/\_\_init\_\_.pypy*

```
#...

from flask import Flask

app = Flask(__name__, static_url_path='')
app.config.from_object('config')

#...

```

**Example: Node.js Application Configuration: **

*<Application-DIRECTORY>/Procfile*

```
web: node web-server.js
```

*<Application-DIRECTORY>/web-server.js*

```
var express = require('express');
var app = express();

app.set('port', (process.env.PORT || 8000));
app.use(express.static(__dirname));

app.get('/', function(request, response) {
   response.sendfile('index.html', {root: __dirname })
});

app.listen(app.get('port'), function() {
  console.log("Node app is running at localhost:" + app.get('port'));
});

```

Note: as the web-server.js deplends on express package, make sure you have config it in your package.json. 


## <a name="deploy-the-app">Deploy the App</a>

In this step you will deploy the app to Heroku, create an app on Heroku first:
	
	$  heroku create
	Creating lit-bastion-5032 in organization heroku... done, stack is cedar-14
	http://lit-bastion-5032.herokuapp.com/ | https://git.heroku.com/lit-bastion-5032.git
	Git remote heroku added

When you create an app, a git remote repo(called heroku) is also created and associated with your local git repository.

Heroku generates a random name (in this case lit-bastion-5032) for your app, or you can pass a parameter to specify your own app name.

Now push to deploy your code:
	
	$ git push heroku master
	Counting objects: 196, done.
	Delta compression using up to 4 threads.
	Compressing objects: 100% (99/99), done.
	Writing objects: 100% (196/196), 33.21 KiB | 0 bytes/s, done.
	Total 196 (delta 82), reused 196 (delta 82)
	Compressing source files... done.
	Building source:

	-----> Python app detected
	-----> Installing runtime (python-2.7.11)
	-----> Installing dependencies with pip
       Collecting dj-database-url==0.3.0 (from -r requirements.txt (line 1))
       Downloading dj_database_url-0.3.0-py2.py3-none-any.whl
       Collecting Django==1.9.1 (from -r requirements.txt (line 2))
       Downloading Django-1.9.1-py2.py3-none-any.whl (6.6MB)
       Collecting gunicorn==19.4.5 (from -r requirements.txt (line 3))
       Downloading gunicorn-19.4.5-py2.py3-none-any.whl (112kB)
       Collecting psycopg2==2.6.1 (from -r requirements.txt (line 4))
       Downloading psycopg2-2.6.1.tar.gz (371kB)
       Collecting whitenoise==2.0.6 (from -r requirements.txt (line 5))
       Downloading whitenoise-2.0.6-py2.py3-none-any.whl
       Installing collected packages: dj-database-url, Django, gunicorn, psycopg2, whitenoise
       Running setup.py install for psycopg2
       Successfully installed Django-1.9.1 dj-database-url-0.3.0 gunicorn-19.4.5 psycopg2-2.6.1 whitenoise-2.0.6

	-----> Preparing static assets
       Running collectstatic...
       ...
       58 static files copied to '/app/staticfiles', 58 post-processed.

	-----> Discovering process types
       Procfile declares types -> web

	-----> Compressing... done, 44.1MB
	-----> Launching...
       Released v4
       http://lit-bastion-5032.herokuapp.com/ deployed to Heroku

	To git@heroku.com:lit-bastion-5032.git
	 * [new branch]      master -> master
 	
In the process of deployment, you may see a syntax error about  gunicorn `invalid syntax for the line yield from self.wsgi.close()`. That error can be ignored. The application is now deployed. Ensure that at least one instance of the app is running:
	
	$  heroku ps:scale web=1
		
Now visit the app at the URL generated by its app name. As a handy shortcut, you can open the website as follows:
	
	$  heroku open

## <a name="view-logs">View Logs</a>


View information about your running app using one of the [logging commands](https://devcenter.heroku.com/articles/logging), heroku logs:

	heroku logs --tail
	2014-08-15T15:17:55.780361+00:00 app[web.1]: 2014-08-15 15:17:55 [2] [INFO] Listening at: http://0.0.0.0:19585 (2)
	2014-08-15T15:17:55.780488+00:00 app[web.1]: 2014-08-15 15:17:55 [2] [INFO] Using worker: sync
	2014-08-15T15:17:55.830489+00:00 app[web.1]: 2014-08-15 15:17:55 [7] [INFO] Booting worker with pid: 7
	2014-08-15T15:17:55.779494+00:00 app[web.1]: 2014-08-15 15:17:55 [2] [INFO] Starting gunicorn 19.0.0
	2014-08-15T15:17:56.321151+00:00 heroku[web.1]: State changed from starting to up
	2014-08-15T15:17:57.847806+00:00 heroku[router]: at=info method=GET path="/" host=lit-bastion-5032.herokuapp.com request_id=7fd99883-20ec-43d5-8b2d-5204351cdf2d fwd="94.174.204.242" dyno=web.1 connect=1ms service=240ms status=200 bytes=679
	
Visit your application in the browser again, and you’ll see another log message generated.


# <a name="example">Example: How Brandgraph Deploy</a>

## <a name="deploy-brandgraph-database-neo4j">Deploy Neo4j to graphaenedb</a>

### <a name="prepare-compress-file-of-brandgraph-database">Prepare Compressed File</a>

- Start neo4j, issue the following command in shell and make sure the link http://localhost:7474/browser/ can be visited normally:

    ```
    $ neo4j console
    ```

- Compress database file `graph.db`, issue the following command in a system shell:

    ```
    $ tar -cjf graph.tar.bz2 %YOUR_NEO4J_DB_PATH%/neo4j-community-2.1.6/data/graph.db
    ```
    Note：%YOUR_NEO4J_DB_PATH% is the address of your personal computer


### <a name="import-the-compress-file-to-graphededb">Import to graphededb</a>

- Login in the site http://app.graphenedb.com/ by following account：

   username: pwc.aia@gmail.com

   password: ******

- Click the `Create database` button：

- Check to`Admin`Tab, click `Restore database` button, click `upload file`, choose the above bz2 file and click `Restore` button, waiting for progress 100%

- Check to `Connection` Tab, and you will see the REST URL, REST USERNAME and REST PASSWORD

## <a name="deploy-brandgraph-api-to-heroku">Deploy API to Heroku</a>
- Make sure have the brandgrph repo, otherwise issue the following command to fetch the latest code:

	```
$ git clone https://github.com/Analytics-Innovation-Incubator/brand_graph.git
	```
    
- Enter the server folder with the following command：

    ```
    $ cd server
    ```  
    
- Replace the deployed address of neo4j database in `config.py` file

    ```
# neo4j address can be accessed on graphenedb if you have create databasein previous step 
NEO4J_DB_ADDR = "http://BrandGraph:rQTeP3MmZHsoq37MipMw@brandgraph.sb06.stations.graphenedb.com:24789/db/data/"
    ``` 
- Modify details of requirements.txt, add following script:

	```
	gunicorn==19.2.1
	```
    
- Define a procfile，and add the following script：

    ```
    web: gunicorn run:app --log-file -
    ``` 
    Note: `run` is the name of starting file and the app is a flask instance in run module.

- Login in heroku with the following account:

    ```
    $ heroku login
    Email: pwc.aia@gmail.com
    Password (typing will be hidden): ******
    ```   
- Create an app on Heroku, which prepares Heroku to receive your source code

    ```
    $ heroku create %APP_NAME%
    ```  
    
	`%APP_NAME%` can be brandgraphapi or brandgraphapidev


    ```
    $ git init
    $ git remote add heroku %HEROKU_REPO_URL%
    ``` 
    %HEROKU_REPO_URL% is create by above `https://git.heroku.com/brandgraphapidev.git` or `https://git.heroku.com/brandgraphapidev.git`.

- Deploy API

    ```
    $ git add .    
    $ git commit -m 'create remote repository'
    $ git push heroku master    
    ```      
- Test after deploy

    ```
    $ heroku ps:scale web=1    
    $ heroku open    

    $ heroku logs --tail # view logs if have any issues：
    ```
    
- Errors may occur

 	```
 	 Error: unsupported locale setting
 	```
 	
  We may use some system variables in local environment, and this would cause error on heroku, the language setting such as `locale.en_us` may cause deploy fail. Just remember search keywords `locale` and comment them temporarily.
 
## <a name="deploy-brandgraph-web-to-heroku">Deploy Web to Heroku</a>

- Enter the web folder by following command:

    ```
    $ cd web
    ```
- Define a procfile，and add the following script：

    ```
    web: node web-server.js
    ```
    
- Modify details of package.json：

    ```
    "express": "4.13.3"
    ```

- Login in heroku by the following account:

    ```
    $ heroku login
    Email: pwc.aia@gmail.com
    Password (typing will be hidden): ******
    ```   

- Create an app on Heroku, which prepares Heroku to receive your source code:

    ```
    $ heroku create %APP_NAME%
    ```
    
    `%APP_NAME%` is brandgraph or brandgraphdev

    ```
    $ git init
    $ git remote add heroku %HEROKU_REPO_URL%
    ``` 
    %HEROKU_REPO_URL% is create by above `https://git.heroku.com/brandgraphdev.git`or `https://git.heroku.com/brandgraphdev.git`.


- Deploy

    ```
    $ git add .
    $ git commit -m 'create remote repository'
    $ git push heroku master
    ```

- Test URL after deploy

    ```
    $ heroku ps:scale web=1
    $ heroku open
    ```



# <a name="reference">Reference</a>

- [Getting Started with Python on Heroku](https://devcenter.heroku.com/articles/getting-started-with-python#deploy-the-app)
- [Getting Started with Node.js on Heroku](https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction)
