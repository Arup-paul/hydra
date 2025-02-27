<!-- <img src='https://img.icons8.com/external-flaticons-lineal-color-flat-icons/344/external-hydra-ancient-greek-mythology-monsters-and-creatures-flaticons-lineal-color-flat-icons.png' width='256px'/> -->

![Hydra - Zero Config API Boilerplate with Laravel Sanctum](https://res.cloudinary.com/roxlox/image/upload/v1653133921/hydra/hydra-trnsparent_jcsl4l.png)

# Hydra - Zero Config API Boilerplate with Laravel Sanctum

[![CircleCI](https://circleci.com/gh/hasinhayder/hydra/tree/master.svg?style=svg)](https://circleci.com/gh/hasinhayder/hydra/tree/master) ![GitHub](https://img.shields.io/github/license/hasinhayder/hydra?label=License&style=flat-square)

Hydra is a zero-config API boilerplate with Laravel Sanctum and comes with excellent user and role management API out of the box. Start your next big API project with Hydra, focus on building business logic, and save countless hours of writing boring user and role management API again and again.

- [Hydra - Zero Config API Boilerplate with Laravel Sanctum](#hydra---zero-config-api-boilerplate-with-laravel-sanctum)
  - [Getting Started](#getting-started)
  - [Database Migration and Seeding](#database-migration-and-seeding)
  - [List of Default Routes](#list-of-default-routes)
  - [Default Roles](#default-roles)
  - [Routes Documentation](#routes-documentation)
    - [User Registration](#user-registration)
    - [User Authentication/Login (Admin)](#user-authenticationlogin-admin)
    - [User Authentication/Login (Other Roles)](#user-authenticationlogin-other-roles)
    - [List Users (Admin Ability Required)](#list-users-admin-ability-required)
    - [Update a User (User/Admin Ability Required)](#update-a-user-useradmin-ability-required)
    - [Delete a User (Admin Ability Required)](#delete-a-user-admin-ability-required)
    - [List Roles (Admin Ability Required)](#list-roles-admin-ability-required)
    - [Add a New Role (Admin Ability Required)](#add-a-new-role-admin-ability-required)
    - [Update a Role (Admin Ability Required)](#update-a-role-admin-ability-required)
    - [Delete a Role (Admin Ability Required)](#delete-a-role-admin-ability-required)
    - [List Available Roles of a User (Admin Ability Required)](#list-available-roles-of-a-user-admin-ability-required)
    - [Assign a Role to a User (Admin Ability Required)](#assign-a-role-to-a-user-admin-ability-required)
    - [Delete a Role from a User (Admin Ability Required)](#delete-a-role-from-a-user-admin-ability-required)
  - [Notes](#notes)
    - [Default Role for New Users](#default-role-for-new-users)
    - [Single Session or Multiple Session](#single-session-or-multiple-session)
    - [Add `Accept: application/json` Header In Your API Calls](#add-accept-applicationjson-header-in-your-api-calls)
  - [Tutorial](#tutorial)
    - [Create a New API Controller](#create-a-new-api-controller)
    - [Add a Function](#add-a-function)
    - [Create Protected Routes](#create-protected-routes)
    - [Test Protected Routes](#test-protected-routes)
    - [Protect a Route with Laravel Sanctum's Ability and Abilities Middleware](#protect-a-route-with-laravel-sanctums-ability-and-abilities-middleware)

## Getting Started

It's super easy to get Hydra up and running.

1. clone the project

```shell
git clone https://github.com/hasinhayder/hydra.git
```

2. install the dependencies

```shell
cd hydra
composer install
```

3. Copy `.env.example` to `.env`

```shell
cp .env.example .env
```

4. Start the webserver

```shell
php artisan serve
```

That's mostly it! You have a fully running laravel installation with Sanctum, all configured.

## Database Migration and Seeding

Open your `.env` file and change the DATABASE options. You can start with SQLite by following these steps

1. Create a new sqlite database

```shell
touch database/hydra.sqlite
```

Or simply create a new file as **hydra.sqlite** inside your **database** folder.

2. Run migration

```shell
php artisan migrate
```

Now your database has essential tables for user and roles management.

3. Database Seeding

Run `db:seed`, and you have your first admin user, some essential roles in the roles table and the relationship properly setup.

```shell
php artisan db:seed
```

Please note that the default admin user is **admin@hydra.project** and default password is **hydra**. You should create a new admin user before deploying to production and delete this default admin user. You can do that using available Hydra user management API, or using any DB management tool.

## List of Default Routes

Here is a list of default routes. Run the following artisan command to see this list in your terminal.

```shell
php artisan route:list
```

![Hydra - List of Default Routes](https://res.cloudinary.com/roxlox/image/upload/v1653131647/hydra/default-routes-hydra_fgn9oh.webp)

## Default Roles

Hydra comes with these `super-admin`,`admin`,`editor`,`customer` & `user` roles out of the box. For details, open the roles table after database seeding, or simply open laravel tinker and experiment with `Role` model

```shell
php artisan tinker
```

run the following command

```php
>>> Role::select(['id','slug','name'])->get()
//or
>>> Role::all(['id','name','slug'])
//or
>>> Role::all()
```

## Routes Documentation

Let's have a look at what Hydra has to offer. Before experimenting with the following API endpoints, run your Hydra project using `php artisan serve` command. For the next part of this documentation, we assumed that Hydra is listening at http://localhost:8000

### User Registration

You can make an `HTTP POST` call to the following endpoint to create/register a new user. newly created user will have the `user` role by default.

```shell
http://localhost:8000/api/users
```

**API Payload & Response**

You can send a Form Multipart payload or a JSON payload like this

```json
{
    "name":"Hydra User",
    "email":"user@hydra.project",
    "passsword":"Surprisingly A Good Password"
}
```

Voila! your user has been created and is now ready to login!

If this user already exists, then you will receive a 409 Response like this

```json
{
    "error": 1,
    "message": "user already exists"
}
```

### User Authentication/Login (Admin)

Remember Hydra comes with the default admin user? You can login as an admin by making an HTTP POST call to the folllowing route

```shell
http://localhost:8000/api/login
```

**API Payload & Response**

You can send a Form Multipart or a JSON payload like this

```json
{
    "email":"admin@hydra.project",
    "passsword":"hydra"
}
```

You will get a JSON response with user token. You need this admin token for making any call to other routes protected by admin ability.

```json
{
    "error": 0,
    "token": "1|se9wkPKTxevv9jpVgXN8wS5tYKx53wuRLqvRuqCR"
}
```

For any unsuccsesful attempt, you will receive a 401 error response.

```json
{
    "error": 1,
    "message": "invalid credentials"
}
```

### User Authentication/Login (Other Roles)

You can login as a user by making an HTTP POST call to the folllowing route

```shell
http://localhost:8000/api/login
```

**API Payload & Response**

You can send a Form Multipart or a JSON payload like this

```json
{
    "email":"user@hydra.project",
    "passsword":"Surprisingly A Good Password"
}
```

You will get a JSON response with user token. You need this user token for making any call to other routes protected by user ability.

```json
{
    "error": 0,
    "token": "2|u0ZUNlNtXgdUmtQSACRU1KWBKAmcaX8Bkhd2xVIf"
}
```

For any unsuccsesful attempt, you will receive a 401 error response.

```json
{
    "error": 1,
    "message": "invalid credentials"
}
```

### List Users (Admin Ability Required)

To list the users, make an `HTTP GET` call to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call.

```shell
http://localhost:8000/api/users
```

**API Payload & Response**

No payload required for this call.

You will get a JSON response with all users available in your project.

```json
[
    {
        "id": 1,
        "name": "Hydra Admin",
        "email": "admin@hydra.project"
    },
    {
        "id": 2,
        "name": "Test User",
        "email": "test@hydra.project"
    },
]
```

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### Update a User (User/Admin Ability Required)

To update an existing user, make a `HTTP PUT` request to the following route. Replace {userid} with actual user id. You must includ a Bearer token obtained from User/Admin authentication. A bearer admin token can update any user. A bearer user token can only update the authenticated user by this token.

```shell
http://localhost:8000/api/users/{userid}
```

For example to update the user with id 2, use this endpoint `http://localhost:8000/api/users/3`

**API Payload & Response**

You can include either `name` or `email`, or both in a URL Encoded Form Data or JSON payload, just like this

```json
{
    "name":"Captain Cook",
    "email":"captaincook@hydra.project"
}
```

You will get a JSON response with user token. You need this user token for making any call to other routes protected by user ability.

```json
{
    "id": 3,
    "name": "Captain Cook",
    "email": "captaincook@hydra.project",
}
```

For any unsuccsesful attempt with invalid token, you will receive a 401 error response.

```json
{
    "error": 1,
    "message": "invalid credentials"
}
```

If a bearer user token attempts to update any other user but itself, a 409 error response will be relivered

```json
{
    "error": 1,
    "message": "Not authorized"
}
```

For any unsuccsesful attempt with invalid `user id`, you will receive a 404 not found error response. For example when you are trying to delete a non existing user with id 16, you will receive the following response.

```json
{
    "error": 1,
    "message": "No query results for model [App\\Models\\User] 16"
}
```

### Delete a User (Admin Ability Required)

To delete an existing user, make a `HTTP DELETE` request to the following route. Replace {userid} with actual user id

```shell
http://localhost:8000/api/users/{userid}
```

For example to delete the user with id 2, use this endpoint `http://localhost:8000/api/users/2`

**API Payload & Response**

No payload is required for this call.

You will get a JSON response with user token. You need this user token for making any call to other routes protected by user ability.

```json
{
   "error": 0,
   "message": "user deleted"
}
```

For any unsuccsesful attempt with invalid token, you will receive a 401 error response.

```json
{
    "error": 1,
    "message": "invalid credentials"
}
```

For any unsuccsesful attempt with invalid `user id`, you will receive a 404 not found error response. For example when you are trying to delete a non existing user with id 16, you will receive the following response. 

```json
{
   "error": 1,
   "message": "No query results for model [App\\Models\\User] 16"
}
```

### List Roles (Admin Ability Required)

To list the roles, make an `HTTP GET` call to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call.

```shell
http://localhost:8000/api/roles
```

**API Payload & Response**

No payload required for this call.

You will get a JSON response with all the roles available in your project.

```json
[
    {
        "id": 1,
        "name": "Administrator",
        "slug": "admin"
    },
    {
        "id": 2,
        "name": "User",
        "slug": "user"
    },
    {
        "id": 3,
        "name": "Customer",
        "slug": "customer"
    },
    {
        "id": 4,
        "name": "Editor",
        "slug": "editor"
    },
    {
        "id": 5,
        "name": "All",
        "slug": "*"
    },
    {
        "id": 6,
        "name": "Super Admin",
        "slug": "super-admin"
    }
]
```

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### Add a New Role (Admin Ability Required)

To list the roles, make an `HTTP POST` call to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call.

```shell
http://localhost:8000/api/roles
```

**API Payload & Response**

You need to supply title of the role as `name`, role `slug` in your payload as Multipart Form or JSON data

```json
{
    "name":"Manager",
    "slug":"manager"
}
```

For successful execution, you will get a JSON response with this newly created role.

```json
{
    "name": "Manager",
    "slug": "manager",
    "id": 7
}
```

If this role `slug` already exists, you will get a 409 error message like this

```json
{
    "error": 1,
    "message": "role already exists"
}
```

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### Update a Role (Admin Ability Required)

To update a role, make an `HTTP PUT` or `HTTP PATCH` request to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call.

```shell
http://localhost:8000/api/roles/{roleid}
```

For example to update the Customer role, use this endpoint `http://localhost:8000/api/roles/3`

**API Payload & Response**

You need to supply title of the role as `name`, and/or role `slug` in your payload as Multipart Form or JSON data

```json
{
    "name":"Product Customer",
    "slug":"product-customer"
}
```

For successful execution, you will get a JSON response with this updated role.

```json
{
    "id": 3,
    "name": "Product Customer",
    "slug": "product-customer"
}
```

Please note that you cannot change a `super-admin` or `admin` role slug because many API routes in Hydra exclusively require this role to function properly.

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### Delete a Role (Admin Ability Required)

To delete a role, make an `HTTP DELETE` request to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call.

```shell
http://localhost:8000/api/roles/{roleid}
```

For example to delete the Customer role, use this endpoint `http://localhost:8000/api/roles/3`

**API Payload & Response**

No payload required for this endpoint.

For successful execution, you will get a JSON response with this updated role.

```json
{
    "error": 0,
    "message": "role has been deleted"
}
```

Please note that you cannot delete the `admin` role because many API routes in Hydra exclusively require this role to function properly.

If you try to delete the admin role you will receive the following 422 error response

```json
{
    "error": 1,
    "message": "you cannot delete this role"
}
```

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### List Available Roles of a User (Admin Ability Required)

To list all available roles for a user, make an `HTTP GET` request to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call. Replace {userid} with an actual user id

```shell
http://localhost:8000/api/users/{userid}/roles
```

For example to get all roles assigned to the user with id 2, use this endpoint `http://localhost:8000/api/users/2/roles`

**API Payload & Response**

No payload is required for this call.

For successful execution, you will get a JSON response containing the user with all asigned roles to it.

```json
{
    "id": 2,
    "name": "Test User",
    "email": "test@hydra.project",
    "roles": [
        {
            "id": 2,
            "name": "User",
            "slug": "user"
        },
        {
            "id": 3,
            "name": "Customer",
            "slug": "customer"
        }
    ]
}
```

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### Assign a Role to a User (Admin Ability Required)

To assign a role to a user, make an `HTTP POST` request to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call. Replace {userid} with an actual user id

```shell
http://localhost:8000/api/users/{userid}/roles
```

For example to assign a role to the user with id 2, use this endpoint `http://localhost:8000/api/users/2/roles`

**API Payload & Response**

You need to supply `role_id` in your payload as Multipart Form or JSON data

```json
{
    "role_id":3 
}
```

For successful execution, you will get a JSON response containing the user with all asigned roles to it.

```json
{
    "id": 2,
    "name": "Test User",
    "email": "test@hydra.project",
    "roles": [
        {
            "id": 2,
            "name": "User",
            "slug": "user"
        },
        {
            "id": 3,
            "name": "Customer",
            "slug": "customer"
        }
    ]
}
```

Notice that user has a `Roles` array and this newly assigned role is present in this array.  

Please note that if you assign the same `role` again to a user, it will have no effect.

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

### Delete a Role from a User (Admin Ability Required)

To delete a role from a user, make an `HTTP DELETE` request to the following route, with Admin Token obtained from Admin Login. Add this token as a standard `Bearer Token` to your API call. Replace `{userid}` with an actual user id, and `{role}` with an actual role id

```shell
http://localhost:8000/api/users/{userid}/roles/{role}
```

For example to delete a role with id 3 from the user with id 2, use this endpoint `http://localhost:8000/api/users/2/roles/3`

**API Payload & Response**

No payload is required for this call

For successful execution, you will get a JSON response containing the user with all asigned roles to it.

```json
{
    "id": 2,
    "name": "Test User",
    "email": "test@hydra.project",
    "roles": [
        {
            "id": 2,
            "name": "User",
            "slug": "user"
        },
    ]
}
```

Notice that user has a `Roles` array and the role with id 3 is not present in this array.  

For any unsuccsesful attempt or wrong token, you will receive a 401 error response.

```json
{
    "message": "Unauthenticated."
}
```

## Notes

### Default Role for New Users

When a new user is created, the `user` role is aassigned to them. To change this behavior, open your `.env` file and set the value of `DEFAULT_ROLE_ID` to any existing role id and newly created users will have that role by default. For example, if you want your new users to have a `customer` role, set `DEFAULT_ROLE_ID=3` in your `.env` file.

### Single Session or Multiple Session

When a user authenticates, Hydra doesn't invalidate the previously issued access token. So, all access tokens including the newly created token will remain balid. If you want to change this behavior and delete all previous tokens when a user authenticates, set `DELETE_PREVIOUS_ACCESS_TOKENS_ON_LOGIN` to `true` in your `.env` file. The value of `DELETE_PREVIOUS_ACCESS_TOKENS_ON_LOGIN` is set to `false` by default.

### Add `Accept: application/json` Header In Your API Calls

This is very important. To properly receive JSON responses, definitely add the following header to your API requests. 

```
Accept: application/json
```

For example, if you are using `curl` you can make a call like this 

```shell
curl --request GET \
  --url http://localhost:8000/hydra/version \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data =
```

## Tutorial

So you decided to give Hydra a try and create a new protected API endpoint, that's awesome, let's dive in. 

### Create a New API Controller

You can create a normal or a resourceful controller. To keep it simple, I am going with a normal controller. 

```shell
php artisan make:controller MessageControler
```

This will create a new file called `app/Http/Controlers/MessageController.php`

### Add a Function

We are going to add a simple function that will greet the authenticated user. Since this will be protected using Sanctum middleware, only a request with a valid bearer token will be able to access this endpoint. You don't need to worry about anything else.

Open this file `app/Http/Controlers/MessageController.php` and add the following code

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class MessageControler extends Controller
{
    public function greet(Request $request){
        $user = $request->user();

        $response = [
            "name"=>$user->name,
            "role"=>$user->roles()->first()->name //or $user->roles()->first()->slug
        ];

        return $response;

    }
}

```

### Create Protected Routes

Let's create a protected route `http://localhost:8000/api/greet` to use this API

Open your `routes/api.php` file and add the following line at the end

```php
Route::get('greet', [MessageControler::class,'greet'])->middleware(['auth:sanctum']);
```

Nice! Now we have a route `/api/greet` that is only accessible with a valid bearer token.

### Test Protected Routes

If you have already created a user then you need his accessToken first, or you can use the admin user or you can simply create a new user and then login and note his/her bearer token. To create or authenticate a user, check the documentation in the beginning.

To create a new user you can place a curl request or use tools like Postman, Insomnia or HTTPie. Here is a quick example using curl.

```shell
curl --request POST \
  --url http://localhost:8000/api/users \
  --header 'Accept: application/json' \
  --header 'Content-Type: multipart/form-data; boundary=---011000010111000001101001' \
  --form 'name=Hydra User' \
  --form email=user@hydra.project \
  --form 'password=Surprisingly A Good Password'
```

Great! now we have our user. Let's login as this new user using curl (You can use tools like Postman, Insomnia or HTTPie)

```shell
curl --request POST \
  --url http://localhost:8000/api/login \
  --header 'Accept: aplication/json' \
  --header 'Content-Type: application/json' \
  --data '{
    "email":"user@hydra.project",
    "password":"Surprisingly A Good Password"
}'
```

Now you have this user's accessToken in the response, as shown below. Note it.

```javascript
{"error":0,"id":2,"token":"5|gbiWdd7yJFYiTIgoK1jK3C7HZJtJUK1PnBIToBLN"}
```

The bearer token for this user is `5|gbiWdd7yJFYiTIgoK1jK3C7HZJtJUK1PnBIToBLN`

Now let's test our protected route. Add this bearer token in your PostMan/Insomnia/HTTPie or Curl call and make a `HTTP GET` request to our newly created protected route `http://localhost:8000/api/greet` . Here's an example call with curl

```shell
curl --request GET \
  --url http://localhost:8000/api/greet \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer 4|uLzGKpLW3xDrWlbERHl39oJLs0kqlq2cgdSHJ1UL'
```

The response will be something like this

```javascript
{
    "name": "user@hydra.project",
    "role": "User"
}
```

Great! you have learned how to create your protected API endpoint using Laravel Sanctum and Hydra!

### Protect a Route with Laravel Sanctum's Ability and Abilities Middleware

Let's make our newly created API endpoint even more robust. Say, we want our route to be accessble by only admin users. Remember you added the following line in `routes/api.php` file just a couple of minutes ago? Let's change it

```php
Route::get('greet', [MessageControler::class,'greet'])->middleware(['auth:sanctum']);
```

Change it like this

```php
Route::get('greet', [MessageControler::class,'greet'])->middleware(['auth:sanctum', 'ability:admin']);
```

Now only a `HTTP GET` call with a valide admin user's access token can access this route. 
If you want this route to be accessible by the users with `admin`, **or** the `user` role, then modify it like this. 

```php
Route::get('greet', [MessageControler::class,'greet'])->middleware(['auth:sanctum', 'ability:admin,user']);
```

If you want this route to be accessible by the users with both `user`, **and** the `customer` role, then modify it like this.

```php
Route::get('greet', [MessageControler::class,'greet'])->middleware(['auth:sanctum', 'abilities:customer,user']);
```

Note that this time we have used the `abilities` keyword instead of `ability`

Great, now you know everything to start creating your next big API project with Laravel & Laravel Sanctum using our powerful boilerplate project called Hydra. Enjoy!
