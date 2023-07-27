# kong-konga-postgres-docker-compose
Docker compose for kong, konga, using postgres database

# how to use
Precondition: Install docker and docker-compose in the server

`
docker-compose up -d
`
<br>After the run we will have
<br>kong run port 8000 (api gateway), 8001 kong admin api
<br>konga run in port 1337<br/>
<br>postgres: 5432<br/>

# how to run
If we run in local host: replace SERVER_IP with localhost
<br>Access to konga usign this
`
http://SERVER_IP:1337/
`
<br>Then first time, you need to register an admin account.

![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/8aa87cf7-8433-48c1-b095-96ac8f420912)

<br> After that you log in again then create a connection that will connect to kong admin (port 8001), Because kong and konga in same network so we can use **http://kong:8001** value
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/6a6e1267-f409-46c3-a1ea-d4a01cac7250)







