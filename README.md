[![CircleCI](https://circleci.com/gh/corneliuze/project-k8s/tree/main.svg?style=svg)](https://circleci.com/gh/corneliuze/project-k8s/tree/main)

This project showcased how to containerize a Machine Learning Microservice API (prediction application) using tools like docker and kubernetes, 
it also showed how to automate build and linting using circleci, Makefiles to tell make what series of commands to run.
A pretrained model has been provided to predict housing prices in Boston according to several features, such as average rooms in a home and data about highway access,
teacher-to-pupil ratios, and so on. You can read more about the data, which was initially taken from Kaggle, on [the data source site](https://www.kaggle.com/c/boston-housing). This project tests your ability to operationalize a Python flask app—in a provided file, `app.py`—that serves out predictions (inference) about housing prices through API calls. This project could be extended to any pre-trained machine learning model, such as those for image recognition and data labeling.

More technical information about the project will be explained.

The Python scripts were generated to create a local virtual environment
   `python3 -m venv ~/.devops` to create the environment.
   `source ~/.devops/bin/activate` to activate the environment.
   `deactivate` to deactivate the environment.

Moving to the Dockerfile `Dockerfile`
 `WORKDIR /app`  Creates a working directory
 `COPY  . app.py /app/` Copies source code to working directory
 `RUN pip install  --no-cache-dir -r requirements.txt` Installs packages from requirements.txt
 `EXPOSE 80` Exposes port 80
 `CMD [ "python3", "app.py" ]`  Runs app.py (which is the web app) at container launch
 
Docker command Scripts `run_doker.sh`
   `docker build --tag=prediction-app  .` Builds a docker image and adds a descriptive tag
   `docker images` Lists docker images
   `docker run -p 8000:80 prediction-app ` Runs flask app  (which is the web app) in the docker container
   
Upload Docker Scripts `upload_docker.sh`
   The docker path is created  `dockerpath=falay/prediction-app` then authentication to docker hub is done using `docker login --username=falay --password=**********`
   after which the image is tagged using the docker path created initially `docker tag prediction-app $dockerpath` finally the image is pushed to the dockerhub using the command `docker push $dockerpath`

Kubernetes Command Scripts `run_kebernetes.sh` 
    
    `kubectl run prediction-app-pod --image=$dockerpath --port=80 --labels app=prediction-app-pod` This command pulls the docker image uploaded initially from dockerhub and creates a kubernetes pod as an abstraction around the container.
    `kubectl get pods` The command lists the pods to get the status while kubernetes creates the pod.   
    `kubectl port-forward prediction-app-pod 8000:80` This commands forward the port of the container to a host.
    
    `make_prediction.sh` contains the HTTP request to call the web app.
    `docker_out.txt` contains the output from docker after running a prediction with `make_prediction.sh`
    `kubernetes_out.txt` contains the output from k8s after running a prediction with `make_prediction.sh`
    `app.py` contains the flask application.
    `Makefile` contains code to compile the web app by setting up the environment, installing dependencies, test, ensure the project files are properly linted 
    `requirements.txt` contains the dependencies that are needed to properly run the project.