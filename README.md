# kong-konga-postgres-docker-compose
Docker compose for Kong, Konga, using Postgres database

# how to use
Precondition: Install docker and docker-compose on the server

`
docker-compose up -d
`
<br>After the run we will have
<br> Kong run port 8000 (API gateway), 8001 Kong admin API
<br>konga run in port 1337<br/>
<br>postgres: 5432<br/>

# How to run
If we run in local host: replace SERVER_IP with localhost
<br>Access to konga usign this
`
http://SERVER_IP:1337/
`
<br>Then first time, you need to register an admin account.

![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/8aa87cf7-8433-48c1-b095-96ac8f420912)

<br> After that you login again and then create a connection that will connect to Kong admin (port 8001), Because kong and Konga in same network so we can use **http://kong:8001** value

![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/6a6e1267-f409-46c3-a1ea-d4a01cac7250)

Now we have connected KongA to Kong.

# Configure Services, Route
## Create Services
<br>We only need to enter the name and URL of service that we will redirect from Kong

![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/6c6dc407-c24f-4de7-b930-2b2f65a7de7e)

## Create Route to service 
<br>In this case we want to route the path **/jsonplaceholder** to service jsonplaceholder

![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/6d6d54d7-c0d2-4a9c-b653-060b3a161c42)

## Test after route
The origin `https://jsonplaceholder.typicode.com/posts`
<br>Then after we using API gateway `http://SERVER_IP:8000/jsonplaceholder/posts`

We can see, they work the same. It means that API gateway worked as we expected.

# Authentication with API gateway


