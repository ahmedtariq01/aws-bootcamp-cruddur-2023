# Week 1 — App Containerization

## Required Homework Tasks


1- Create a new GitHub repo.
- Launch the repo within a Gitpod workspace
- Configure Gitpod.yml configuration, eg. VSCode Extensions
- Clone the frontend and backend repo

![repo and gitpod setup](assets/week1/gitpod.jpg)

2- I Installed the requirements for the backend app to run locally using the following command

```
pip3 install -r requirements.txt
```
![installing the requirements](assets/week1/installingreq.jpg)

3- I ran the app locally using the following command

```
python3 -m flask run --host=0.0.0.0 --port=4567
```
![running the app locally](assets/week1/apprun01.jpg)

- I checked the app is running on port 4567

![port on which app is running](assets/week1/portcheck01.jpg)

- I checked the app on the browser and it is running but giving the following error

![app is running but getting error](assets/week1/err01.jpg)

4- I set up the environment variables for the app using the following command

```
export FRONTEND_URL="*"
export BACKEND_URL="*"
```
- I ran the app locally using the following command

```
python3 -m flask run --host=0.0.0.0 --port=4567
```
![setting up the environment variables for the app](assets/week1/setupvariables.jpg)

- I checked the app on the browser and it is running but giving the following error
![app is running but getting error](assets/week1/err01.jpg)

- Then I appended the url to `/api/activities/home` and this time app is running and return the json response. I got the following response
```
https://4567-ahmedtariq0-awsbootcamp-faaa5sb9a9t.ws-us87.gitpod.io/api/activities/home
```

![app running](assets/week1/apprunning01.jpg)

- After that I removed the environment variables using the following commands

```
unset FRONTEND_URL="*"
unset BACKEND_URL="*"
```
-- Checked the environment variables are removed or not using the following command

``` 
env | grep FRONTEND_URL
env | grep BACKEND_URL
```
![environment variables removed](assets/week1/varremoved.jpg)

- As the app is running locally now I will create a Dockerfile for the backend app and run the app in container

5- I created a Dockerfile for the backend app here `backend-flask/Dockerfile`. Dockerfile is as follows

```
FROM python:3.10-slim-buster

# Inside container
# Create a directory for the app
WORKDIR /backend-flask

# Outside container -> Inside container
# libraries required for the app
COPY requirements.txt requirements.txt

# Inside container
# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside container -> Inside container
# . means everything in the current directory
# . . means everything in the parent directory
COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

6- I built the container for the backend app using the following command

```
docker build -t  backend-flask ./backend-flask
```

![building the container](assets/week1/build.jpg)

- I checked the image is created or not using the following command

```
docker images
```
![checking the build images](assets/week1/images.jpg)



![running the app in container](assets/week1/runapp02.jpg)

![getting errow when running the app in container](assets/week1/err02.jpg)


![setting up the environment variables for the app in container](assets/week1/setupvar.jpg)

![access the logs of the app in container](assets/week1/checklogs.jpg)

![checking the logs of the app in container](assets/week1/logs.jpg)


![running the app in container](assets/week1/runapp03.jpg)

![app running in container](assets/week1/apprunning02.jpg)



Explore the codebases

docker images

Ensure we can get the apps running locally

Write a Dockerfile for each app

Ensure we get the apps running via individual container

Create a docker-compose file

Ensure we can orchestrate multiple containers to run side by side

Mount directories so we can make changes while we code




# Week 1 — App Containerization

## References

Good Article for Debugging Connection Refused
https://pythonspeed.com/articles/docker-connection-refused/


## VSCode Docker Extension

Docker for VSCode makes it easy to work with Docker

https://code.visualstudio.com/docs/containers/overview

> Gitpod is preinstalled with theis extension

## Containerize Backend

### Run Python

```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to `/api/activities/home`
- you should get back json



### Add Dockerfile

Create a file here: `backend-flask/Dockerfile`

```dockerfile
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

### Build Container

```sh
docker build -t  backend-flask ./backend-flask
```

### Run Container

Run 
```sh
docker run --rm -p 4567:4567 -it backend-flask
FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask
unset FRONTEND_URL="*"
unset BACKEND_URL="*"
```

Run in background
```sh
docker container run --rm -p 4567:4567 -d backend-flask
```

Return the container id into an Env Vat
```sh
CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)
```

> docker container run is idiomatic, docker run is legacy syntax but is commonly used.

### Get Container Images or Running Container Ids

```
docker ps
docker images
```


### Send Curl to Test Server

```sh
curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"
```

### Check Container Logs

```sh
docker logs CONTAINER_ID -f
docker logs backend-flask -f
docker logs $CONTAINER_ID -f
```

###  Debugging  adjacent containers with other containers

```sh
docker run --rm -it curlimages/curl "-X GET http://localhost:4567/api/activities/home -H \"Accept: application/json\" -H \"Content-Type: application/json\""
```

busybosy is often used for debugging since it install a bunch of thing

```sh
docker run --rm -it busybosy
```

### Gain Access to a Container

```sh
docker exec CONTAINER_ID -it /bin/bash
```

> You can just right click a container and see logs in VSCode with Docker extension

### Delete an Image

```sh
docker image rm backend-flask --force
```

> docker rmi backend-flask is the legacy syntax, you might see this is old docker tutorials and articles.

> There are some cases where you need to use the --force

### Overriding Ports

```sh
FLASK_ENV=production PORT=8080 docker run -p 4567:4567 -it backend-flask
```

> Look at Dockerfile to see how ${PORT} is interpolated

## Containerize Frontend

## Run NPM Install

We have to run NPM Install before building the container since it needs to copy the contents of node_modules

```
cd frontend-react-js
npm i
```

### Create Docker File

Create a file here: `frontend-react-js/Dockerfile`

```dockerfile
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

### Build Container

```sh
docker build -t frontend-react-js ./frontend-react-js
```

### Run Container

```sh
docker run -p 3000:3000 -d frontend-react-js
```

## Multiple Containers

### Create a docker-compose file

Create `docker-compose.yml` at the root of your project.

```yaml
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```

## Adding DynamoDB Local and Postgres

We are going to use Postgres and DynamoDB local in future labs
We can bring them in as containers and reference them externally

Lets integrate the following into our existing docker compose file:

### Postgres

```yaml
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
```

To install the postgres client into Gitpod

```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

### DynamoDB Local

```yaml
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

Example of using DynamoDB local
https://github.com/100DaysOfCloud/challenge-dynamodb-local

## Volumes

directory volume mapping

```yaml
volumes: 
- "./docker/dynamodb:/home/dynamodblocal/data"
```

named volume mapping

```yaml
volumes: 
  - db:/var/lib/postgresql/data

volumes:
  db:
    driver: local
```
