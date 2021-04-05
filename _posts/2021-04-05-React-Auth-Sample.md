---
layout: post
title:  "React Authentication Sample"
date:   2021-04-05 12:33:16 +0200
categories: [projects, app]
tags: [projects, app]

---
 [![React-Auth-Backend CI](https://github.com/spydx/react-auth-sample/actions/workflows/react-auth-backend.yml/badge.svg)](https://github.com/spydx/react-auth-sample/actions/workflows/react-auth-backend.yml)
[![React-Auth-Frontend CI](https://github.com/spydx/react-auth-sample/actions/workflows/react-auth-frontend.yml/badge.svg)](https://github.com/spydx/react-auth-sample/actions/workflows/react-auth-frontend.yml)
[![Docker Integration Tests](https://github.com/spydx/react-auth-sample/actions/workflows/docker-integration-tests.yml/badge.svg)](https://github.com/spydx/react-auth-sample/actions/workflows/docker-integration-tests.yml)

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=spydx_react-auth-sample&metric=alert_status)](https://sonarcloud.io/dashboard?id=spydx_react-auth-sample)
[![Code Smells](https://sonarcloud.io/api/project_badges/measure?project=spydx_react-auth-sample&metric=code_smells)](https://sonarcloud.io/dashboard?id=spydx_react-auth-sample)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=spydx_react-auth-sample&metric=bugs)](https://sonarcloud.io/dashboard?id=spydx_react-auth-sample)

Small sample project with React frontend and a simple Rust backend.

Repo: [React-Auth-Sample](https://github.com/spydx/react-auth-sample)

## Goals of this project

- Write a Rust API with [Actix.rs](https://actix.rs)
- Write Rust Tests for the API
- Create a simple React app in TypeScript that has a Sign In / Register and Sign out 
- Write React tests with Jest
- Use integration tests [Cypress](https://cypress.io)
- Use [Docker](https://docker.com) to perform local integration tests
- Use GitHub Actions to automate testing

## Running the sample

It is several ways to run this project.

### Using Docker

Simple commands:

```sh
$ docker-compose up
```

Then open your favorite browser to [react-auth-sample](http://localhost:80)

### Manual

To do this version, you will have to have `rust build tools` and `yarn` installed.

```sh
react-auth-backend $ cargo run
```

```sh
react-auth-frontend $ yarn install
react-auth-frontend $ yarn start
```

Then open your favorite browser to [react-auth-sample](http://localhost:80)

## Running Integration Tests

To run the interactive integration tests in Cypress do the following:

```sh
$ docker-compose -d up
$ cd e2e/
$ yarn install
$ export "CYPRESS_baseUrl=http://localhost/"
$ ./nodes_modules/.bin/cypress open
```

To run the integration tests via docker:

```sh
$ docker-compose -f docker-compose.test.yml up --exit-code-from cypress
```

Example video
[![Integration test video](/assets/img/react-auth-sample/preview.png)](https://user-images.githubusercontent.com/16806653/113511926-9095c480-9562-11eb-8527-890c74682165.mp4)



## Documentation

### Backend

The backend has three endpoints:

- POST `/api/auth/register` - register an account in the system
- POST `/api/auth/login` - log in the registerd user
- GET `/api/accounts/` - view all accounts on the system

The last endpoint is just there for making it easier to debug,
being able to show all registerd users in the system.

The backend only stores the users in memory, and will discard them if the service is restarted.

An account is consisting of a users name, email and password.
The password is hashed with [Argon2](https://www.argon2.com/) and stored in the service. The hash should be stores in a DB, but that is out of scope for this service.

When a uses is logging in, the user supplies it's email and password, and will get a [JSON Web Token](https://jwt.io/) generated that is stored in the React app (Frontend) so the users is authenticated, this token gets discarded when the user logges out.

CORS has been configured in the [Actix Web Framwork](https://actix.rs) for Rust.
It's configured to allow all headers, origins and methods.
Not recommended, but will suffice for our test environment here.

There is no option to delete users.

### Register

Takes in a JSON message constructed like this:

```json
{
   "name": "yourname",
   "email": "email@email.ee",
   "password":"yoursecretpassword"
}
```

Example:

```sh
$ curl http://localhost:8080/api/auth/register \\
   -H "Content-type:application/json" \\
   -d "{ \"name\":\"yourname\", \"email\":\"email@mail.ee\", 
   \"password\": \"password\" }"
```

Returns this to you

```json
{
   "name":"yourname",
   "email":"email@mail.ee"
}
```

It is not good pratice to return the password hash.

## Login

Takes in a JSON message constructed like this:

```json
{
   "email": "email@email.ee",
   "password":"password"
}
```

Example:

```sh
$ curl http://localhost:8080/api/auth/login \\
   -H "Content-type:application/json" \\
   -d "{ \"email\":\"email@mail.ee\", \"password\": \"password\" }"
```

That returns this content to you:

```json
{  
   "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJlbWFpbEBtYWlsLmVlIiwiZXhwIjoxNjE3NDgyMzQ0fQ.PCrcAFXyQPM42wY82KaDnhMyp85AUg-LpEqJiqOL7aD28au84o53pUTImkR3m4GSLjDUGdyFpTokZPwOJ30tZw"
}
````

## Resources

- [End-to-End Testing Web Apps: The Painless Way](https://mtlynch.io/painless-web-app-testing/)
- [mtlynch/hello-world-cypress](https://github.com/mtlynch/hello-world-cypress/)
- [Cypress.io](https://cypress.io)
- [Rust](https://rust-lang.org)
- [Rust Book](https://doc.rust-lang.org/book/)
- [Actix Web Framwork](https://actix.rs)
- [Docker](https://docker.com)
- [Docker Crash Course](https://github.com/spydx/docker-crashcourse)
