# python-flask-docker
This is a simple tutorial on how to Dockerize a Python Flask App

## Prerequsites:
### Docker
```
$ docker -v
Docker version 18.09.2, build 6247962
```

### Python
```
$ python --version
Python 2.7.10
```

### Virtualenv
```
$ vitualenv --version
16.4.3
$ vitualenv venv
$ source venv/bin/activate
(venv) $
$ source venv/bin/deactivate
```

### Flask
```
$ flask --version
Flask 1.0.2
Python 2.7.10 (default, Aug 17 2018, 19:45:58) 
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.0.42)]
```

### Account at https://hub.docker.com/

## Part 1: Setup Steps
### 1.1: Create a folder to hold the project
```
$ mkdir python-flask-docker
```
### 1.2: Navigate to that directory
```
$ cd python-flask-docker
```
## Part 2: Create the Python Flask Hello World Application
### 2.1: Create the app.py file
```
$ vi app.py
```
```
# app.py - a minimal flask api using flask_restful
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

class HelloWorld(Resource):
    def get(self):
        return {'hello': 'world'}

api.add_resource(HelloWorld, '/')

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

### 2.2: Create the requirements.txt file
```
$ vi requirements.txt
```
```
flask
flask_restful
```

### 2.3: Create the Dockerfile
```
$ vi Dockerfile
```
```
# Dockerfile - this is a comment. Delete me if you want.
FROM python:2.7
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
ENTRYPOINT ["python"]
CMD ["app.py"]
```

## Part 3: Let's Build, Tag, Run our app locally
### 3.1: Build and tag our docker image
```
$ docker build -t python_flask_docker:latest .
```
```
...
Successfully built cf8265dd7d82
Successfully tagged python_flask_docker:latest
```

### 3.2: View Docker Images
```
$ docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
python_flask_docker   latest              cf8265dd7d82        19 seconds ago      921MB
python                2.7                 92c086fc9702        2 weeks ago         914MB
```

### 3.3: Run the build
```
$ docker run -d -p 5000:5000 python_flask_docker:latest
ad04385ef6dc57709b5ec58af705fd5b7f1fb465bed3a8bab68428450a8fee44
```

### 3.4: View Containers Running
```
$ docker ps
CONTAINER ID        IMAGE                        COMMAND             CREATED             STATUS              PORTS                    NAMES
ad04385ef6dc        python_flask_docker:latest   "python app.py"     9 seconds ago       Up 8 seconds        0.0.0.0:5000->5000/tcp   cranky_elion
```

### 3.5: View the Restful API locally using CURL, Browser or Postman
```
$ curl http://127.0.0.1:5000/
{
    "hello": "world"
}
```

## Part 4: Let's Push our Image to Docker Hub
### 4.1: Login to Docker Hub
```
$ docker login -u jrdalino
Password:
Login Succeeded
```

### 4.2: Let's re-tag the image with our username prefix
```
$ docker tag python_flask_docker jrdalino/python_flask_docker
```

### 4.3: Push the image to Docker Hub
```
$  docker push jrdalino/python_flask_docker

```

## Part 5: Let's stop and remove our local docker instance and pull it back from Docker Hub
### 5.1: Let's get the Container ID of docker container
```
$ docker ps
CONTAINER ID        IMAGE                        COMMAND             CREATED             STATUS              PORTS                    NAMES
ad04385ef6dc        python_flask_docker:latest   "python app.py"     3 minutes ago       Up 3 minutes        0.0.0.0:5000->5000/tcp   cranky_elion
```

### 5.2: Let's kill our container
```
$ docker kill ad04385ef6dc
ad04385ef6dc
```

### 5.3: Remove all docker images
```
$ docker system prune -a
```

### 5.4: Our docker image is gone
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

### 5.5: Let's pull back our image from Docker Hub
```
$ docker pull jrdalino/python_flask_docker
...
Status: Downloaded newer image for jrdalino/python_flask_docker:latest
```

### 5.6: We should have our image back
```
$ docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
jrdalino/python_flask_docker   latest              cf8265dd7d82        8 minutes ago       921MB
```

### 5.7: Let's run it now
```
$ docker run -d -p 5000:5000 jrdalino/python_flask_docker:latest
71218cbf84422b53238416b69417135cb540f2b8c1f350b9d4f4b23ed1cc0ef5
```

### 5.8: It is alive again
```
$ docker ps
CONTAINER ID        IMAGE                                 COMMAND             CREATED             STATUS              PORTS                    NAMES
71218cbf8442        jrdalino/python_flask_docker:latest   "python app.py"     8 seconds ago       Up 7 seconds        0.0.0.0:5000->5000/tcp   gracious_tereshkova
```

### 5.9: View the Restful API locally using CURL, Browser or Postman
```
$ curl http://127.0.0.1:5000/
{
    "hello": "world"
}
```

## And we're done!
