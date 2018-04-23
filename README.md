# MNIST Service with Cassandra database

This project uses Tensorflow's MNIST dataset to train and test the model. Using a trained model to create a program that recognizes handwritten digits, and then publishes service by using Flask, deploys the service to a docker container, and stores the data in a database by connecting a Cassandra container.


# Instructions

## Install Docker

If you don't have Docker already, download and install Docker from [here](https://docs.docker.com/install/).

## Prepare the model
You can use model_maker.py to create the model. In this project, I used model4 to make the recognization program.
Notice: If you create your own model, you should change the path in app.py in order to use your model. 

     saver.restore(sess, "YOUR_OWN_MODEL_PATH")

## Deploy MNIST-service in Docker

Build the image in Docker.

    $ docker build -t IMAGE_NAME .
Run the app. I mapped my machine's port 4000 to the container's published port 5000.

    $ docker run -d -p 4000:5000 IMAGE_NAME
You can also check [the docker tutorial](https://docs.docker.com/get-started/part2/) for this part.


## Start a Cassandra server instance 
Change `some` to any name you want:

    $ docker run --name some-cassandra -p 9042:9042 -d cassandra:latest
Check the status of the node via the following command:

    $ docker exec some-cassandra nodetool status
Make sure the *Address* it shows is same as *contact_points* in the app.py.

    cluster = Cluster(contact_points=["172.17.0.6"],port=9042)

## Test the app

Go to that URL(http://localhost:4000/) in a web browser. Press the ***Browse...*** button, choose the test file, press ***Upload*** button and then you can see your result.

## Manage the database

This app will create Keyspace and table automatically, and insert data as well. You can change the Keysapce and table name in app.py  if you want.

You can manage the database via:

     $ docker run -it --link some-cassandra:cassandra --rm cassandra cqlsh cassandra
You can check [here](https://www.tutorialspoint.com/cassandra/index.htm) for more details of managing distributed database.

## Remove service

You can check container id via:

    $ docker container ls
Then remove container by:

    $ docker container stop CONTAINER_ID
    $ docker container rm CONTAINER_ID
You can check image id via:

    $ docker image ls
Then remove it by:

    $ docker image rm IMAGE_ID
or:

    $ docker image rm -f IMAGE_ID

