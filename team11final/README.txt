DESCRIPTION -

	First, please go to www.OffBeatApp.com
	This is what we have been working on

	Hello User. Offbeat is a cloud deployed webapp that takes song data scraped from Spotify,
uses your tastes in music from your spotify history, and matches you to songs based on how well you 
want the recommendations to perform. Unlike traditional recommendation engines that blindly match you to
the songs of users who listen to the same songs you do, Offbeat lets you choose how close or how far you
want your recommendations to be based on the features of the music itself. OffBeat lets you discover music
that would otherwise be off the beaten path.

	Over the past few months, Team Eleven has spent a lot of time developing and 
deploying OffBeat. Some of the technologies OffBeat leverages are cloud based (Google Cloud) and 
therefore will not be available in a local deployment. If you would like to deploy a local version 
of Offbeat, we encourage you to create a local MySQL database or use your favorite onine hosting 
platform, but know that the code and filepaths will need to be adapted.

	This package contains all of the code required to perform all of the analytics involved 
in Offbeat, as well as all of the table create statements for MySQL, and all of the WebFramework 
deployment code in Django. It does not contain instructions on using the gcloud ui for set up and
configuration of clusters and database instances. Some basic console commands will be provided.


INSTALLATION - 

To run this project you will need a google cloud account and need to set up the following:

-Google cloud web app instance (Not immediately necessary, is configured through App.yaml)
Type: F4
Memory: 512MB
CPU Limit: 2.4GHz

Songs scraping VMs (2): 
Instance Type: N1
CPUs: 1
Memory 3.75GB

MySQL Database
Version: 5.7
Disk Space Used: 5GB

The CODE Directory is set up in the following way:
CODE
|___1-data_storage
|	|____create_tables.sql
|___2-data_gathering
|	|____config.py
|	|____getArtists.py
|	|____getSongs.py
|___3-analytics
|	|____AgglomClass.ipynb
|	|____Analytics_Update.md
|	|____Feature_Exploration.ipynb
|___4-model
|	|____Offbeatr.py
|	|____README.md
|___5-webfront
|	|____static
|	|	|____circles.css
|	|	|____d3.v5.min.js
|	|	|____d3-dsv.min.js
|	|	|____d3-fetch.min.js
|	|	|____d3-scale-chromatic.v1.min.js
|	|	|____d3-simple-slider.min.js
|	|	|____d3-tip.min.js
|	|	|____qt.pickle
|	|	|____script.js
|	|____Templates
|	|	|____index.html
|	|____WebFront
|	|	|____ __pycache__
|	|	|	|____ settings.cpython-37.pyc
|	|	|	|____ urls.cpython-37.pyc
|	|	|	|____ views.cpython-37.pyc
|	|	|	|____ wsgi.cpython-37.pyc
|	|	|____ .DS_Store
|	|	|____asgi.py
|	|	|____settings.py
|	|	|____urls.py
|	|	|____views.py
|	|	|____wsgi.py
|	|____.gitignore
|	|____app.yaml
|	|____manage.py
|	|____requirements.txt
|___6-cloud_deployment
	|____deploy.sh

	We have ordered these in the order they should be deployed and run. 
Once you have procured your SQL cluster, find the connect string, and whitelist your local Ip address to run code
locally, or run the create_tables.sql to build the necessary tables. Your database is now "Installed"

If deploying on cloud, cloning the github repo should be sufficient to install, as deploying the app will install 
all of the necessary librarys in your requirements.txt in your virtual enviroment. Running a django make_migrations 
is NOT necessary for this project as it does not have user memory or authentication.

Running this project will also require that you set up a Spotify developers account and regiter an App in
in order to access the spotify developers API. add your App keys to the .config file

EXECUTION -

Directories were ordered the way they should be used. 

Data storage:
	Once the SQL database is created and the VM started, you can run the create_tables.sql in the database. This will create the tables necessary for storage.

Data Gathering:
	Next, in your virtual machine instance, run the GetArtists.py. This will scrape spotify for the top streaming 
artists and store them in the database. Once This has finished running, run the GetSongs.py. This will use the Spotify IDs of the artists you have scraped to pull all of the songs relevant to those artists, collecting their complete musical features.

Analytics & Model:
	The preprocessing, distribution transformation, clustering, labeling, and calculation of centroids are performed using a single file. Run Offbeatr.py to generate local .csv files of the labeled song data and centroids. The script takes about ~2.5 hours to complete. The files are then uploaded to the songs_labeled and centroids tables in the SQL database where they are used by the front end. For additional information on the script arguments see the README file in the ./4-model/ directory. 


WebFront:
	Offbeat was deployed as a single page webapp. The server is configured and set up using Django, which hosts
two primary URLS. the first is the entry point URL which displays a single html page (called index.html). To run the server in
your cloud directory, simply use the bas console to navigate to the Webfront (./5-webfront/WebFront) and run the command
Python3 manage.py runserver 8000. This will run a version in your cloud platforms localhost.

	The app gathers data about each user's Spotify usage by first gaining access to the user's data via the Implicit Grant authorization flow (https://developer.spotify.com/documentation/general/guides/authorization-guide/#implicit-grant-flow). Then, song data is obtained via the Personalization (https://developer.spotify.com/documentation/web-api/reference/personalization/) and Tracks (https://developer.spotify.com/documentation/web-api/reference/tracks/) endpoints of the Spotify API. 
	After data is gathered, d3.js (https://d3js.org/) is used to show a bar chart summary of the user's listening habits broken down by different audio features. The user can then click an "Explore" button to generate personalized recommendations for a variety of levels of similarity to their habits. The results are drawn via d3.js, this time in the format of zoomable circle packing. Nested circles in the svg correspond to the agglomerative cluster hierarchy of the model results, and random songs from appropriate clusters are shown in the front end. In each bottom-level cluster, the user can distinguish song similarity via a gradient coloring scale, and they can hover over any individual song node to launch an iframe Spotify widget and play the song.

Cloud Deployment:
	We have included a shell script that we used for deploying quickly for iterative development. Running this script will
reclone the github repo the project is hosted on, install the requirements.txt and the run gcload app deploy for you. It's just
faster than remembering all the correct console commands in the correct order.

--CREDENTIALS--
For your convenience, we provide here the credentials needed to interact with the tool:

Google Cloud
username: 	TeamElevenProject@gmail.com
password: 	Projectime

Spotify Premium
username:	TeamElevenProject@gmail.com
password:	Projectime

Please feel free to use the provided credentials to log in and view our google cloud account, or to log into the offbeatapp if you don't have your own spotify. Thank you!
