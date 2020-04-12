# go-web-play


## Introduction

This is a two-step code challenge with the following **GOAL**:

    Deliver a small and simple RESTful-ish API for managing users



## Guiding Principles

* The application server should be working.
* Show how you would write unit and/or functional tests for your application.
* Store data however you want: a json file, csv, sqlite, mysql or whatever you want. As long as the tests are passing and you have a functional application.
* Deliver as many of the user stories below as possible. But do not
  worry about getting all of them done. The focus here is to see your
  workflow.
* **Preferably** write your tests before writing application code, you will be asked about your thought process.
* You **must** deliver a functional test suite to prove that your RESTful API works. (look at existing functional tests for examples)


## Setting up your environment

You will need:

- golang 1.13 or superior
- Operating system: Linux or MacOSX (no support for windows)



#### Tests


- Unit tests cannot perform I/O, instead they should mock things.
- Functional tests are allowed to perform real I/O (such as database calls, or writing files to disk), but should clean up after themselves.

##### Running tests

Run unit tests with

```bash
make unit
```

And functional tests with

```bash
make functional
```


##### Running the server

```bash
make run
```


### Story 1: Create a simple endpoint that generates an md5 hash of an arbitrary email

```gherkin
Given the arbitrary email address "user@ddd.com"
When I POST a json payload with '{"data": "user@ddd.com"}' to the URL "/api/calculate-md5"
Then it should return a json that looks like this: '{"md5": "8c3a7ee05457d337d5bb14f438464cbf"}'
```

### Story 2: Build API endpoint that allows user creation

```gherkin
Given a JSON payload containing the fields:
  | field      | value            |
  | first_name | Guido            |
  | email      | guido@python.org |
  | password   | py123            |
When I POST on "/api/user"
Then it should store the user and return a JSON response with the fields:
  {
    "first_name": "Guido",
    "uuid": "02fd0bf3d9518a801517fd6fac005878",
    "email": "guido@python.org"
  }
And it should be stored in some sort of database
```

    Notes:
      1. Generate the UUID with the pseudo-algorithm:
         uuid = md5digest("test:guido@python.org")

      2. Store password securely.

### Story 3: Build API endpoint for editing users

```gherkin
Given that the following users exist in the databse:
  | first_name | email            | password  |
  | Guido      | guido@python.org | py123     |
When I PUT on "/api/user/02fd0bf3d9518a801517fd6fac005878" with the a JSON payload containing:
  {
    "password": "anotherpw"
  }
Then the database should have the users:
  | first_name | email            | password   |
  | Guido      | guido@python.org | anotherpw  |
And it should return a JSON response with the fields:
  {
    "first_name": "Guido",
    "uuid": "02fd0bf3d9518a801517fd6fac005878",
    "email": "guido@python.org"
  }
```

### Story 4: Build API endpoint for retrieving a user

```gherkin
Given that the following users exist in the databse:
  | first_name | email            |
  | Guido      | guido@python.org |
When I GET on "/api/user/02fd0bf3d9518a801517fd6fac005878"
Then it should return a JSON response with the user data
  {
    "first_name": "Guido",
    "uuid": "02fd0bf3d9518a801517fd6fac005878",
    "email": "guido@python.org"
  }
```

### Story 5: Build API endpoint for retrieving a list of users

```gherkin
Given that the following users exist in the databse:
  | first_name | email            |
  | Guido      | guido@python.org |
  | Foo        | foo@bar.com      |

When I GET on "/api/users"
Then it should return a JSON response with a list of the existing users
```

### Story 6: Build API endpoint for removing users

```gherkin
Given that the following users exist in the databse:
  | first_name | email            | password  |
  | Guido      | guido@python.org | py123     |
  | Foo        | foo@bar.com      | foobar123 |
When I DELETE on "/api/user/guido@python.org"
Then it should return the deleted user info in the response
And now the database should only contain the users:
  | first_name | email            | password  |
  | Foo        | foo@bar.com      | foobar123 |
```
