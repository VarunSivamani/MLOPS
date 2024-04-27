# Session - 1

## Assignment
 

1. Create Dockerfile that uses https://github.com/rwightman/pytorch-image-modelsLinks to an external site.

2. Build the image for this

3. Create an Inference Python Script that takes a model name and image path/url and outputs json like 

```
{"predicted": "dog", "confidence": "0.89"}
```

4. MODEL and IMAGE must be configurable while inferencing

5. Model Inference will be done like: 

```
docker run $IMAGE_NAME --model $MODEL --image $IMAGE
```

6. Push the Image to Docker Hub

7. Try to bring the docker image size as less as possible (maybe try out slim/alpine images?) (use architecture and linux specific CPU wheel from here -  [link](https://download.pytorch.org/whl/torch_stable.html))

8. Pull from DockerHub and run on Play With Docker to verify yourself

9. Submit the Dockerfile contents and your complete image name with tag that was uploaded to DockerHub, also the link to the github classroom repository

10. Tests can be run with 

```
bash ./tests/all_tests.sh
```

<br>

# Solution - Steps

1. Build the Image

```
docker build --tag <image_name> .
```

2. List all the containers
```
docker ps -a
```

3. List all images
```
docker images
```

4. View running containers
```
docker ps
```

5. Get details about a container
```
docker inspect <container_name or container_id>
```

6. Go inside running container
```
docker exec -it <container> bash 
```

7. Push to DockerHub
```
docker push <username>/<image_name or image_id>
```

<br>

## Results

```
REPOSITORY        TAG       IMAGE ID       CREATED          SIZE
mlops-01-small    latest    2f7617b99f1d   18 seconds ago   817MB
mlops-01-medium   latest    9cc2a09e9404   59 seconds ago   864MB
mlops-01-huge     latest    d2a408446ee8   7 minutes ago    1.09GB
```