# Dockerization and Local Deployment

- I have installed Docker Desktop in my local to view my Application images and containers locally.


- I have created a Docker file for containerizing the Application, and here, I have used multi-staged Docker file.



 _Command used to build the Docker Image from Docker file locally_

            docker build -t smileappimage .

_Command used to run the container locally from the image that was built_

            winpty docker run -p 8000:8000 -it srivyshnavi01/smileappimage:latest
- I have used winpty as I am running these commands in the Windows system. Also, I have exported container port to host port to make the application run in my local.


_Command to check the app in the container that was created_

            winpty docker exec -it 3d92351712bd(container id) bash

_Pushed my docker image to docker hub using the below command_

            docker image push srivyshnavi01/smileappimage:latest
