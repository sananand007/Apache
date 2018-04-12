## Steps to run Apache Hadoop and Mapreduce Job on Docker (windows) with Cloudera CDH Docker image using Python env

### Installing Docker
+ Get the Docker windows CE installtion
+ Currently I am just running docker on my windows PC , eventually will migrate to AWS
+ most of important information is at : https://docs.docker.com/ and https://docs.docker.com/install/
+ Get the Cloudera Docker container : https://www.cloudera.com/documentation/enterprise/latest/topics/quickstart_docker_container.html
+ I choose docker because it is way faster than using a VM on my PC and does not have the feel of a VM but as if you are running everythong on a command prompt
+ Using a container - https://medium.com/@nagarwal/lifecycle-of-docker-container-d2da9f85959

## After Installing Docker , Run docker image
+ Docker should be running after installation , create username and password so that you can log in the api
+ check if docker is active using the below commands and start the container

```(python)
docker --version
Docker version 18.03.0-ce, build 0520e24

docker pull cloudera/quickstart:latest #get the latest Cloudera image

docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
cloudera/quickstart   latest              4239cd2958c6        2 years ago         6.34GB

#run the image with the below command to start the container
docker run --hostname=quickstart.cloudera --privileged=true -t -i -p 8888:8888 -p 80:80 cloudera/quickstart /usr/bin/docker-quickstart

```
+ After staring the container you will be logged in the linux bash

```(python)
[root@quickstart /]# ls
[root@quickstart /]# exit #stops this container instance
```

+ Running a haddop job using mapper.py and reducer.py
  + make sure that the mapper.py runs locally with the python version that your container has
  + take help from here : http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/
  + once you have made sure that mapper.py works and compiles

```
[root@quickstart /]# python -m py_compile reducer.py
[root@quickstart /]# python -m py_compile mapper.py
[root@quickstart /]# cat purchases.txt | python /mapper.py

### Text file output that I have
Glendale        343.86
Baton Rouge     398.51
New Orleans     347.68
St. Louis       321.16
Seattle 3.63
Milwaukee       89.69
Tampa   310.8
Oakland 287.48
Albuquerque     3.21
Baton Rouge     252.61
Chesapeake      146.68
Lubbock 97.92
Aurora  188.72
Chicago 107.89
St. Louis       317.65
Scottsdale      19.95
Raleigh 385.72
Kansas City     205.06
Pittsburgh      475.32

[root@quickstart /]# hadoop jar /usr/lib/hadoop-0.20-mapreduce/contrib/streaming/hadoop-streaming-2.0.0-mr1-cdh4.1.1.jar -file /mapper.py -mapper /mapper.py -file /reducer.py -reducer /reducer.py -input myInput/* -output jobOutput
```

+ After successfully running the job the output directory should have the below:

```
[root@quickstart /]# hadoop fs -ls jobOutput
Found 2 items
-rw-r--r--   1 root supergroup          0 2018-04-12 16:37 jobOutput/_SUCCESS
-rw-r--r--   1 root supergroup       2296 2018-04-12 16:37 jobOutput/part-00000
```
