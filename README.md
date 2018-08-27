# Pyshiny

An easy to deploy Python API for Shiny apps using Flask.

## Initial Setup

To run this as smoothly as possible, you'll need Docker. 

* To install Docker on Windows and Mac follow this [link](https://www.docker.com/get-started).
* To install Docker on Ubuntu follow this [tutorial](https://docs.docker.com/install/linux/docker-ce/ubuntu/). 
* Create a Docker account [here](https://hub.docker.com/). This might not be mandatory but for sure will be useful for you at some point.

## Get started with Pyshiny

First of all, clone the repo, open your terminal, navigate to the **pyshiny** folder, and follow the next steps.

#### 1. Create an image from the Dockerfile

In the Dockerfile you'll find that we're using https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/ image and we're adding two dependencies: 

* `psycopg2` for PostgreSQL
* `pandas` for data wrangling

You may add or remove dependencies as you wish.

To create a new image run: `docker build -t pyshiny .` .

#### 2. Create a new container with a volume bind.

Our new container needs to have a volume bind so you can edit the code without destroying and rebuilding the container.

On your terminal run:

* `docker container run -d -p 80:80 -v /path/to/pyshiny/app:/app --name pyshinycont pyshiny`
* **OR**  `docker container run -d -p 80:80 -v $(pwd)/app:/app --name pyshinycont pyshiny` (not sure if it works on a Windows machine).
* After creating your container go to your browser and type `localhost`. You should see a message saying **"Still Alive!"**. You're container is now running on port 80.

#### 3. Configure the env.py file.

This server is meant to have only server-to-server communication over **https**. In order to authenticate itself, the requesting server (a Shiny app in this case) needs to send a **parameter in the header named token** with a key that the Pyshiny server will validate. If you want to read abour how to send this data on the header, you can find that information [here](https://rdrr.io/cran/httr/man/add_headers.html).

Going back to the env.py file...

1. Go to your **/app** directory and rename the env.rename file to **env.py**
2. Create a secure token and fill the `token = ""` line with your new key. You can easily create one [here](https://randomkeygen.com/).
3. If you're using a database you can add the DB credentials to your file.
4. Feel free to add any other settings your server needs.
5. Save the file and stop the container by running on the terminal `docker stop pyshinycont`.
6. Start your container again by running `docker start pyshinycont -i` if you're developing or `docker start pyshinycont` if you're deploying on production.

You're all set now. Go to Postman (or similar), send a GET or POST request to `localhost/myroute` with the token on the header and you should see **"Allow"** on your screen.

You now have a running REST server that you can use to add Python functionality to your Shiny App. We hope to keep extending the repo with more stuff in the near future.

