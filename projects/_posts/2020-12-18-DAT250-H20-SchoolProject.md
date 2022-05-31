---
layout: post
title:  "DAT250 H20 School Project: PollHub"
date:   2020-12-18 12:33:16 +0200
tags: [projects, app, springboot, java, react, rust, flutter, iot]
---

**Repo:** [dat250gruppe9](https://github.com/spydx/dat250gruppe9)

## Members

[Andrè Frøseth Jønland](https://github.com/ImGoze)

[Jan-Erik Erstad](https://github.com/Jan-Erik-Erstad)

[Kenneth Fossen](https://github.com/spydx)

[Rune Almåsbakk](https://github.com/runalmaas)

## PollHub

It is a cloud-based voting service that supports IoT devices.
It has been built with several components.

- Backend
   - SpringBoot
   - MySQL
   - JPA/Hibernate
- Messaging
   - RabbitMQ
- NoSQL
   - MongoDB
- FrontEnd
   - React JS
- IoT Device
   - Flutter
- Microservice
   - Rust

![Overview of backend](/assets/img/dat250/backend.png)

## Design Document

We created an initial design of the application and documented it here.

[Design: High-level application design and persistence ](https://github.com/spydx/dat250gruppe9/blob/main/DesignDocument/designdocument.md)

## Prototyping

We made two prototypes for the project, 
one for the Persistence of the data in the application and the second was a early prototype for our backend.

[Prototyping: High-level application persistence](https://github.com/spydx/dat250gruppe9/tree/main/dat250-jpa-designproto)

Persistent prototyping for the first assignment creating our datamodel for the app.

[Design and Prototyping: Business logic and REST API ](https://github.com/spydx/dat250gruppe9/tree/main/dat250-restapi-proto)

Second assignment where we designed and implemented the RESTAPI w/swagger and the businesslogic in Services.

## PollHub (FeedApp)

To spin up this setup you have to have installed [Docker](https://www.docker.com/products/docker-desktop)
Make sure you are in the folder that holds the [docker-compose.yml](docker-compose.yml) file.

This spins up 7 containers:

```sh
kenneth@kefo ~/dat250gruppe9> docker-compose up -d
Starting dat250gruppe9_feedapp-iotdevice_1 ... done
Starting dat250gruppe9_feedapp-db_1        ... done
Starting dat250gruppe9_feedapp-messaging_1 ... done
Starting dat250gruppe9_cloud-mongodb_1     ... done
Starting dat250gruppe9_feedaapp-frontend_1 ... done
Starting dat250gruppe9_feedapp-muservice_1 ... done
Starting dat250gruppe9_feedapp-api_1       ... done
kenneth@kefo ~/dat250gruppe9> 
```

* FeedApp FrontEnd =>[http://localhost](http://localhost/) ([Use a browser with disabled CORS for local testing](#cors-disabled-browser-chrome))
* FeedApp API =>[http://localhost:8080](http://localhost:8080/)
* FeedApp MySQL => [mysql://localhost:3306](mysql://localhost:3306)
* Cloud MongoDB =>[mongodb://feedapp:mongo@localhost:27017/feedapp/](mongodb://feedapp:mongo@localhost:27017)
* FeedApp Messaging (guest/guest) => [http://localhost:15672](http://localhost:15672)
* FeedApp IoT Device => [http://localhost:81](http://localhost:81/) ([Use a browser with disabled CORS if using the web version](#cors-disabled-browser-chrome))
* FeedApp MuService => use the Docker Console to view it.

This will create a default setup that has a the following user installed.

* admin@pollhub.no / admin
* iot {iot-test-device} / 1234, username is hardcoded to the "device"
* mongodb: feedapp /mongo
* mysql: feedapp /feedapppassword

To configure the admin password for the service.
Do this in the [application.properties](dat250-feedapp-api/src/main/resources/application.properties)

## Final Presentation for the Project

[Final Presentation](https://github.com/spydx/dat250gruppe9/blob/main/ProgressPresentation/Software_Technology_Project_-_Final_Presentation_-_PollHub.pdf)

## API Documentation

Sample sett of JSON examples against the API.

## HTTP GET/POST/PUT/DELTE

All `GET` is allowed from all users.
`POST` is allowed to `/api/auth/*`
`POST, PUT, DELETE` is only for authenticated users.

### Login

Request:

```http
POST http://localhost:8080/api/auth/login
Content-Type: application/json
Cache-Control: no-cache

{
  "email": "kenneth@mail.com",
  "password" : "test123"
}
```

Response:

```http
POST http://localhost:8080/api/auth/login

HTTP/1.1 200
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: application/json
Transfer-Encoding: chunked
Date: Tue, 27 Oct 2020 18:35:32 GMT
Keep-Alive: timeout=60
Connection: keep-alive

{
  "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIzZGNmODdlYS02MjNmLTQ4ZTYtOGE2OS03YTdjN2E0MDhiMzUiLCJpYXQiOjE2MDM4MjM3MzIsImV4cCI6MTYwNDQyODUzMn0.AQYL_bD8oXBKgawfZN8vLHwOZUcyMJLFWMRw-Fg9CltFPU2ubPdAEXu0cCxM0KWJtzg3nA1V0K7EyLwH36hosg",
  "tokenType": "Bearer",
  "profile": "c58414bf-efa6-42d7-91a9-a735422ccd2d"
}
```

### Register user

```http
POST http://localhost:8080/api/auth/register
Content-Type: application/json
Cache-Control: no-cache

{
  "email": "kenneth@mail.com",
  "password" : "test123",
  "firstname": "Kenneth",
  "lastname": "Fossen"
}
```

Response:

```http
POST http://localhost:8080/api/auth/register

HTTP/1.1 201 
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
Location: http://localhost:8080/api/users/c58414bf-efa6-42d7-91a9-a735422ccd2d
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: application/json
Transfer-Encoding: chunked
Date: Tue, 27 Oct 2020 18:22:01 GMT
Keep-Alive: timeout=60
Connection: keep-alive

{
  "success": true,
  "msg": "Registred"
}
```


### Change password

To be able to do this, you need to know the ProfileID for the user to be updated.
This situation it is : `ProfileID: e4b8ed12-8cca-4f64-8e3f-efbe195589c7`

Also, the user has to be authenticated with a JWT Bearer token, 
to be able to send the update. This is the HTTP HEADER Option:
`Authorization: Bearer <token>`

```http
PUT http://localhost:8080/api/account/e4b8ed12-8cca-4f64-8e3f-efbe195589c7
Content-Type: application/json
Cache-Control: no-cache
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhY2IxOTQxZi0xYjBhLTQ0OTQtYjAxYy0xMjBjYzBjMjQ2YzAiLCJpYXQiOjE2MDM1NzkwNTAsImV4cCI6MTYwNDE4Mzg1MH0.FB0UAS3MEuIGh4ff4o7vFQy--JDP0oQYHlPQ3arjLdj2COgDLbTkluXnaD-oH1cG120XuioclTGjRmSIpzXckA

{
  "email": "kenneth@mail.com",
  "password" : "newpassword"
}
```

### Create a Poll

**Req:**

* Authenticated
* Owner ID is deduced from JWT Bearer

This is the HTTP HEADER Option: `Authorization: Bearer <token>`

Request:

```http
POST http://localhost:8080/api/polls/
Content-Type: application/json
Cache-Control: no-cache
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI4MzFmODNmZi05NjFjLTQ2OWEtYjgzZC0wN2JjNDIwNmQ0MjMiLCJpYXQiOjE2MDM2MDgyNDcsImV4cCI6MTYwNDIxMzA0N30.98tnkeGCiwvZwbBuFt5URpObmgCznUtWjQXVnTQK2u_IvSCeuSjS4ILkL8PZVK4Tb3FCrjDwdNbFHy3q5ZrWJA

{
  "access": "PUBLIC", //can also be PRIVATE or HIDDEN
  "answerno": "stringed",
  "answeryes": "stringa",
  "name": "StringyStringString",
  "question": "stringy?",
  "timeend": "2020-10-22T21:54:07.062Z",
  "timestart": "2020-10-30T21:54:07.062Z" //has been removed
}
```

`PUBLIC` anyone can vote on it
`PRIVATE` only for registered users
`HIDDEN` only for members that know the pollID

Response:

```http
[
  {
    "id": "2a80286a-65fd-4c41-8f0a-e0dc79a48427",
    "name": "StringyStringString",
    "question": "stringy?",
    "timestart": null,
    "timeend": "2020-10-22T21:54:07.000+00:00",
    "access": "PUBLIC",
    "answeryes": "stringa",
    "answerno": "stringed",
    "owner": {
      "id": "e4b8ed12-8cca-4f64-8e3f-efbe195589c7",
      "firstname": "Kenneth",
      "lastname": "Fossen"
    }
  }
]
```

### Get All (Public)

```http
GET http://localhost:8080/api/polls/
Accept: application/json
```

Returns only `"access": "PUBLIC"` polls

### Get All Polls logged in

Req `Authorization: Bearer <token>`

```http
GET http://localhost:8008/api/polls/
Accept: application/json
Authorization: Bearer <token>

```

Returns only `PUBLIC` and `PRIVATE` and no `HIDDEN`

Result:

```http
[
  {
    "id": "2a80286a-65fd-4c41-8f0a-e0dc79a48427",
    "name": "StringyStringString",
    "question": "stringy?",
    "timestart": null,
    "timeend": "2020-10-22T21:54:07.000+00:00",
    "access": "PUBLIC", <-- PUBLIC
    "answeryes": "stringa",
    "answerno": "stringed",
    "owner": {
      "id": "e4b8ed12-8cca-4f64-8e3f-efbe195589c7",
      "firstname": "Kenneth",
      "lastname": "Fossen"
    }
  },
  {
    "id": "706a63ec-82e9-4593-b7ad-6550a26b71cd",
    "name": "StringyStringString",
    "question": "stringy?",
    "timestart": "2020-10-26T23:06:26.000+00:00",
    "timeend": "2020-10-22T21:54:07.000+00:00",
    "access": "PRIVATE", <-- PRIVATE
    "answeryes": "stringa",
    "answerno": "stringed",
    "owner": {
      "id": "e4b8ed12-8cca-4f64-8e3f-efbe195589c7",
      "firstname": "Kenneth",
      "lastname": "Fossen"
    }
  }
]

Response code: 200; Time: 3492ms; Content length: 657 bytes
```

### Get a poll by ID

```http
GET http://localhost:8080/api/polls/5facb988-def9-4e69-bbd0-676b517ddd9e
Accept: application/json
```

### Get a poll Owner

```http
GET http://localhost:8080/api/polls/bae6e5c5-839f-4f8a-98f1-5d2aabf6fd9c/owner
Accept: application/json
```

### Get a result for a Poll

```http
GET http://localhost:8080/api/polls/bae6e5c5-839f-4f8a-98f1-5d2aabf6fd9c/result
Accept: application/json
```

### Retrive all Users Profiles

```http
GET http://localhost:8080/api/poll/
Accept: application/json

```

### Update profiledetails (not password)

Req: `userid` and `Authorization: Bearer <token>`

```http
PUT http://localhost:8080/api/users/e4b8ed12-8cca-4f64-8e3f-efbe195589c7
Content-Type: application/json
Cache-Control: no-cache
Authorization: Bearer <token>

{
  "firstname": "Kenneth",
  "lastname": "stringY"
}
```

Response:

```http
{
  "id": "e4b8ed12-8cca-4f64-8e3f-efbe195589c7",
  "firstname": "Kenneth",
  "lastname": "stringY"
}
```
### Update Account Password

Req: `accountid` and `Authorization: Bearer <token>`

```http
PUT http://localhost:8080/api/account/{{accountId}}
Content-Type: application/json
Cache-Control: no-cache
Authorization: Bearer <token>

{
  "email": "string",
  "password": "string"
}
```

Result will return complete profile
```http
HTTP/1.1 200 
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Access-Control-Request-Headers
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 28 Oct 2020 18:19:21 GMT
Keep-Alive: timeout=60
Connection: keep-alive

{
  "id": "52fdcf98-1afb-44c5-ac66-be081d337f66",
  "email": "kenneth@mail.com",
  "profile": {
    "id": "848d28d5-4185-4cac-a635-6ae868ae5dd6",
    "firstname": "Kenneth",
    "lastname": "Fossen",
    "votedOn": []
  },
  "roles": [
    {
      "id": 1,
      "name": "ROLE_USER"
    }
  ]
}
```

### Vote on a Poll

`PollID` for the poll to Vote on.
Will deduce the User from JWT Token in the HTTP HEADER Option: `Authorization: Bearer <token>`

If no token is found, you will be able to vote on polls that are public.
Only Public polls are open for anonymous voting.

```http
POST http://localhost:8080/api/poll/353e1708-6ccb-43b2-843e-3328a2451e55/vote/
Content-Type: application/json
Cache-Control: no-cache
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiI4MzFmODNmZi05NjFjLTQ2OWEtYjgzZC0wN2JjNDIwNmQ0MjMiLCJpYXQiOjE2MDM2MDgyNDcsImV4cCI6MTYwNDIxMzA0N30.98tnkeGCiwvZwbBuFt5URpObmgCznUtWjQXVnTQK2u_IvSCeuSjS4ILkL8PZVK4Tb3FCrjDwdNbFHy3q5ZrWJA

{
  "answer": false
}
```
# IoT device in action

Built in Flutter.
Makes the app portable to many devices
(iOS, Android, Web, macOS, Windows App, Linux App)

To build for iOS you have to have a Apple Product.
Same goes for macOS (the build has to happen there)
Same goes for Windows App and Linux App.
You have to build on its repective plattform.

## Screenshots of IoT

![login](/assets/img/dat250/login.png){: width="250" }
![vote](/assets/img/dat250/vote.png){: width="250" }
![log](/assets/img/dat250/log.png){: width="500" } 

## Device in action

[Demo video from app](/assets/img/dat250/iot-device.mov)

## MuService in action

microService that subscribes to messages from FeedApp API.

The service is build using [Rust](www.rust-lang.org)
Fast and good language to build services in.

the subcription is looping forever waiting for messages, 
It passes async jobs to new threads to post data onto Dweetio.
Result data is dumped in a _cloud mongodb_ database.
We are simulating this connection in this project.

The FeedApp API pushes these selfexplanatory messages:

* FEEDAPP_NEWPOLL
* FEEDAPP_CLOSED
* FEEDAPP_RESULT

### DweetIO

`FEEDAPP_NEWPOLL` and `FEEDAPP_CLOSED` are messages that are being pushed to
[Dweetio](https://dweet.io)

The opening and closing is pushed as events to this system.
The `secret` identifier for our thing is: `be4106c3-bd56-40ca-9a5a-c1cb0c0bf8cc`.

All events can be listened to on this address:

[https://dweet.io/dweet/for/be4106c3-bd56-40ca-9a5a-c1cb0c0bf8cc](https://dweet.io/dweet/for/be4106c3-bd56-40ca-9a5a-c1cb0c0bf8cc)

### Cloud MongoDb NoSQL database

All messages comming in with `FEEDAPP_RESULT` will be parsed,
and pushed to to mongodb cloud.
It the messages doesn't fit the `struct PollResult` it will fail silently in the service.

Since we are simulating a mongodb cloud, we are storing the result for further analysis in a "local" database, the content is sent throug messages from the API => RabbitMQ => MuService => Cloud MongoDb.

Every document is stored in `resultcol` in the `feedapp` database,
that the user `feedapp` has access to.

### Console Log Example

This is an example with some verbose logging on the muService.

![consolelog](/assets/img/dat250/muservice_console.png)