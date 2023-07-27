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
We can add an authentication plugin at services or routes. I will guide you to add in the route level.
## Basic authentication
<br>From route -> plugins -> add -> basic authentication

![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/51a5c0e8-876a-4120-8269-758d197e1520)

<br> Create a new consumer to handle basic authentication, 
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/f6478524-a42b-4f10-8c77-eb27458012a1)

<br> Create a basic authentication credential 
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/cf830748-d048-486d-ab52-5842fb39803c)

### Test it
<br>If we don't provide any credentials, there is an error like this. 
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/26e06d78-fe5c-459f-8612-215f393ed66f)

<br>We can use Postman to create a credential for us
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/93b5bfd3-7c4e-4295-ae32-3b05f65865e3)

<br> It will auto add authentication header for us
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/e5b5e00a-9bd6-4c0d-a471-90054d30355e)

<br>We also can manually add it. the structure is easy 
<br>the value = `Basic base64(username:password)`
<br>we can get base64 online https://www.base64encode.org/
<br>For example: username: **tommy**, password: **123456**
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/ae41aa85-802d-4e6b-82cf-a1985f4ae1e9)
<br> base64("tommy:123456") =dG9tbXk6MTIzNDU2
<br> Authenication: Basic dG9tbXk6MTIzNDU2

## Key authentication
<br>Create a key authentication in the route
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/2018fe4d-dcff-4926-918e-b6e2b0e6ee5d)

<br> Go to Consumer, and create a key authentication credential.
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/b9ea5423-144e-45f6-a62b-584fd6f1cb4f)

We can add it in Postman to check
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/7ffaea46-eca7-493c-b327-5af7b606c2a7)
<br>
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/2dcd0ef7-6b49-4357-9610-a15e6ea6e3ea)


## HMAC Authentication
<br> Create an HMAC authentication in the route
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/ff74770a-464f-4133-ba1e-e744a7d038a8)

<br> Create an HMAC credential in the consumer
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/ceeb7063-cce1-4b0e-bea2-4dbb1522ba0e)

Using HMAC credentials, is more complicated than 2 above ones
we can follow the guides 
<br>https://ranggasama.medium.com/hmac-authentication-using-kong-api-gateway-e6d3020fa1f9
<br>https://docs.konghq.com/hub/kong-inc/hmac-auth/

Use javascript to calculate `date` and `authorization` values in pre-request script of Postman
```
const path = '/jsonplaceholder/posts';
const username = 'hmac-user';
const algorithm = 'hmac-sha256';
const method = 'GET';
const secret = 'secret-value';
var dateFormat = new Date().toUTCString();
pm.request.headers.add({
    key: 'date',
    value: dateFormat
})
var signingString = 'date: ' + dateFormat + '\n' + method + ' ' + path + ' HTTP/1.1';
var sha256 = CryptoJS.HmacSHA256(signingString, secret);
var signature = CryptoJS.enc.Base64.stringify(sha256);
console.log('signingString ' + signingString);
console.log('signature ' + signature);
authorization = 'hmac username="' + username + '", algorithm="' + algorithm + '", headers="date request-line", signature="' + signature + '"';
pm.request.headers.add({
    key: 'authorization',
    value: authorization
})
```
It autos add 2 headers data and authorization to header request<br>
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/98cc177c-4c5f-4289-b29d-377f2f536a88)
Open the console, we can see the added headers<br>
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/0403bc09-a085-4b0d-abe2-525c3395c31c)


<br> Remember to change value **data**, **url**, **username**, **secret**, **uuid** and **algorithm**
<br> Run `node index.js`

Put data and authorization in the header of the request
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/d60fa7d2-14cd-45d5-8291-0726cd232eb5)

## JWT Authentication
Add JWT authentication at the route 
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/3f2d5c0f-82ab-45ea-adb2-f6bcdd990371)

Add JWT authentication in consumer credential
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/48572c41-4ed6-4553-ba69-4f0d36bb71f6)

And run the test in Postman
![image](https://github.com/ledangtuanbk/kong-konga-postgres-docker-compose/assets/5629901/3c7ee3a8-791e-47ca-84fc-007612a490e1)








