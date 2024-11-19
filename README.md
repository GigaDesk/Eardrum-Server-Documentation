# Eardrum-Server-Documentation
This project provides a detailed documentation of eardrum-server API. Overally, eardrum-server is a school management system, that enable schools manage data that includes, teachers, students and other school entities. It leverages the following technologies:
* **GraphQL** : The project leverages the GraphQL data query and manipulation language for its API that allows a client to specify what data it needs.
* **OTP**    : The project leverages One-Time-Password for multi-factor authentication (MFA) process that requires the user to enter a code sent to their phone number alongside their account credentials.

## Endpoint
The API only includes a single endpoint: https://eardrum-423271079010.europe-west3.run.app

## Query Types
The API includes the following query types: 

### schoolPhoneNumberExists:
The `schoolPhoneNumberExists` query type executes following tasks:
* Checking if a school's phone number entry already exists
* Checking the state in which the phone number exists. In this case, whether it is verified or not.

A verified phone number is one that has been submitted by a school that went on to complete the multi-factor authentication (MFA) process, by entering the correct OTP 
sent to their phone number. On the other hand, an unverified phone number is one that the school is yet to complete MFA process.

The `schoolPhoneNumberExists` query type takes the following arguments:
* `phone_number` : The specific phone number that one wishes to check its existence. The argument must follow the following rules:
  * It must be a `string` data type.
  * It cannot be omitted (non-nullable).
  * It must be in [E.164](https://www.twilio.com/docs/glossary/what-e164) format

An example is: "+254123456789"

The `schoolPhoneNumberExists` returns the type `PhoneNumberExists` that includes the following fields:  
* `verified` : `Boolean`. Returns `true` if verified.
* `unverified`: `Boolean`. Returns `true` if unverified.

Here is a sample query:

 ```
 GraphQL

  query checkPhoneNumber {   
     schoolPhoneNumberExists( phone_number: "+254123456789" ) {    
        verified   
        unverified   
      }    
  }
```
Here is a sample response:   

```
JSON

{
  "data": {
    "schoolPhoneNumberExists": {
      "verified": false,
      "unverified": false
    }
  }
}
```
### getSchoolProfile     
A `getSchoolProfile` query performs the primary task of retrieving a schools profile information. To make a `getSchoolProfile` query, the school needs to be logged in an thus make a request with the header bearing a token in its **Authorization** field.  
It does not take any argument and returns the following `SchoolProfile` type with the following fields:    
  * `createdAt`: The specific time of the school profile's creation. `Time` and non-nullable
  * `updatedAt`: The specific time when the school profile was updated. Also, includes the time it was created. `Time` and non-nullable
  * `name` : The name the school. `string` and non-nullable. 
  * `phone_number`: The phone number of the school. `string` and non-nullable
  * `badge` : The image url of the logo of the school. `string` and nullable.
  * `Website`: The website url of the school. `string` and nullable.

Here is a sample query:    
#### Header    

```
{
    "Authorization": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MzE4NjIwNzQsImlkIjoiMSIsInJvbGUiOiJzY2hvb2wifQ.K3rUjOd3rVnos1ShCOqAUqhXDILQGvJglZ5uoGwCC0k"
}
```
#### Body    

```
query SchoolProfile{
  getSchoolProfile{
    createdAt
    updatedAt
    name
    phone_number
    badge
    Website
  }
}
```
Here is a sample response:     

```
{
  "data": {
    "getSchoolProfile": {
      "createdAt": "2024-11-15T12:23:04.704399+03:00",
      "updatedAt": "2024-11-15T12:23:04.704399+03:00",
      "name": "Kisii School",
      "phone_number": "+254123456789",
      "badge": null,
      "Website": "www.kisiischool.ac.ke"
    }
  }
}
```
## Mutation Types
The API includes the following mutation types: 

### createSchool:
The `createSchool` mutation type executes following tasks:
* Signs up anew school to the system.
* Sends out an OTP code for phone number verification.

The `createSchool` query type takes the following arguments:
* `input` : The new school's data which is of `NewSchool` data type, that includes the following fields:
  * `name`: represents name of the school. `string` and non-nullable
  * `phone_number`: represents the school's official phone number. `string` and non-nullable. Must be in E.164 format
  * `password`: represents the school's password.`string` and non-nullable
  * `badge` : represents the schools logo's image url. `string` and nullable
  * `Website`: represents the school's website url.`string` and nullable

The `createSchool` returns the type `UnverifiedSchool` that includes the following fields:  
  * `id` : The school record's unique primary key. `integer` and non-nullable
  * `createdAt`: The specific time of the school record's creation. `Time` and non-nullable
  * `updatedAt`: The specific time when the school record was updated. Also, includes the time it was created. `Time` and non-nullable
  * `deletedAt`: The specific time when the school record was deleted.`SoftDelete` and nullable
  * `name` : The name the school created. `string` and non-nullable. 
  * `phone_number`: The phone number of the school created. `string` and non-nullable
  * `password` : The hashed password of the school created. `string` and non-nullable
  * `badge` : The image url of the logo of the school created. `string` and nullable.
  * `Website`: The website url of the school created. `string` and nullable.

Here is a sample mutation:   

```
GraphQL

 mutation createSchool {   
    createSchool( input: {name: "Kisii School", phone_number: "+254123456789", password:"Kisii@2024", Website:"www.kisiischool.ac.ke"} ) {    
      id
      createdAt
      updatedAt
      deletedAt
      name
      phone_number
      password
      badge
      Website
     }    
 }
```
Here is a sample response:   

```
JSON

{
  "data": {
    "createSchool": {
      "id": 1,
      "createdAt": "2024-11-15T12:05:02.906434553+03:00",
      "updatedAt": "2024-11-15T12:05:02.906434553+03:00",
      "deletedAt": null,
      "name": "Kisii School",
      "phone_number": "+254123456789",
      "password": "$2a$14$0pT2rHYKopCGIdEO5.XeQOnspwBGgpbvoWRpdTBLzG5GwwmL910W6",
      "badge": null,
      "Website": "www.kisiischool.ac.ke"
    }
  }
}
```
***Note:*** After the mutation the user (a school in this case) should expect an OTP verification code sent to their phone number.

### verifySchool   
The `verifySchool` mutation type performs the primary task of transforming a created school record that is yet to be verified into a verified school record.    
The `verifySchool` mutation type takes an `input` argument that has the following fields:
  * `phone_number`: represents the school's phone number that was entered in a `createSchool` mutation. `string` and non-nullable. Must be in E.164 format
  * `otp`: represents the OTP code that was sent to the school's phone number.`string` and non-nullable.

The `verifyschool` returns the type `School` that includes the following fields:  
  * `id` : The school record's unique primary key. `integer` and non-nullable
  * `createdAt`: The specific time of the school record's creation. `Time` and non-nullable
  * `updatedAt`: The specific time when the school record was updated. Also, includes the time it was created. `Time` and non-nullable
  * `deletedAt`: The specific time when the school record was deleted.`SoftDelete` and nullable
  * `name` : The name the school created. `string` and non-nullable. 
  * `phone_number`: The phone number of the school created. `string` and non-nullable
  * `password` : The hashed password of the school created. `string` and non-nullable
  * `badge` : The image url of the logo of the school created. `string` and nullable.
  * `Website`: The website url of the school created. `string` and nullable.

Here is a sample mutation:
```
GraphQL

 mutation verifySchool {   
    verifySchool(input:{phone_number: "+254123456789", otp:"181794"}) {    
      id
      createdAt
      updatedAt
      deletedAt
      name
      phone_number
      password
      badge
      Website
     }    
 }
```
Here is a sample response:
```
JSON

{
  "data": {
    "verifySchool": {
      "id": 1,
      "createdAt": "2024-11-15T12:23:04.70439981+03:00",
      "updatedAt": "2024-11-15T12:23:04.70439981+03:00",
      "deletedAt": null,
      "name": "Kisii School",
      "phone_number": "+254123456789",
      "password": "$2a$14$0pT2rHYKopCGIdEO5.XeQOnspwBGgpbvoWRpdTBLzG5GwwmL910W6",
      "badge": null,
      "Website": "www.kisiischool.ac.ke"
    }
  }
}
```
### sendCode      
The `sendCode` mutation type performs the primary task of sending an OTP code to a phone number under the following scenarios:    
  * A phone number that expects an OTP is yet to receive one.
  * A phone number received an OTP code but for some reason it got lost

The `sendCode` mutation type takes a `phone_number` argument and returns a `SendCodeStatus` that has the following fields:
  * `phone_number` : The phone number that should expect an OTP code. `string` and non-nullable
  * `success`: Whether the `sendCode` mutation was successful. `boolean` and non-nullable.

Here is a sample mutation: 
```
GraphQL

 mutation sendCode {   
    sendCode(phone_number: "+2547123456789") {    
      phone_number
      success
     }    
 }
```
Here is a sample response:
```
JSON

{
  "data": {
    "sendCode": {
      "phone_number": "+254123456789",
      "success": true
    }
  }
}
```
Once the school receives the OTP code, it can proceed to perform a `verifySchool` mutation.
### schoolLogin     
The `schoolLogin` mutation performs the task of logging a user, in this case a school, into the system. It takes a `phone_number` and a `password` and returns an token of type `string` that identifies the user once they are logged in.      

Here is a sample mutation:

```
mutation schoolLogin{
  schoolLogin(input:{phone_number:"+254123456789", password:"Kh@1934"})
}
```
Here is a sample response:
```
{
  "data": {
    "schoolLogin": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MzE4NjI4NDYsImlkIjoiMSIsInJvbGUiOiJzY2hvb2wifQ.v1J0311KutT4YoAL32KbrLSX82E6iBR82KLPK_nkxxQ"
  }
}
```
### forgotSchoolPassword      
The `forgotSchoolPassword` mutation helps a school to recover its account in the event that they lost their `password`. It takes a `phone_number` sends an OTP code to the school and returns a `sendCodeStatus`.      
Here is a sample mutation:     

```
mutation forgotSchoolPassword{
  forgotSchoolPassword(phone_number:"+254123456789"){
    phone_number
    success
  }
}
```
Here is a sample response:    

```
{
  "data": {
    "forgotSchoolPassword": {
      "phone_number": "+254123456789",
      "success": true
    }
  }
}
```
Once the school has received an OTP code, they can proceed to `requestSchoolPasswordReset`. If they fail to receive the code they can resend the code with a `sendCode` mutation.     
### requestSchoolPasswordReset    
The `requestSchoolPasswordReset` mutation verifies that the school that forgot its password owns the phone number that the OTP code was sent to. It takes the school's `phone_number` and the `otp` code sent to the school.     
It returns a token of type `string` that the school that allows them to perform a `resetSchoolPassword` mutation.
Here is a sample mutation:    
```
mutation requestSchoolPasswordReset{
  requestSchoolPasswordReset(input:{phone_number:"+254123456789", otp:"457796"})
}
```
Here is a sample response:
```

{
  "data": {
    "requestSchoolPasswordReset": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MzE4NjIwNzQsImlkIjoiMSIsInJvbGUiOiJzY2hvb2wifQ.K3rUjOd3rVnos1ShCOqAUqhXDILQGvJglZ5uoGwCC0k"
  }
}
```
### resetSchoolPassword     
The `resetSchoolPassword` updates the school's `password`. It takes a `new_password` and requires an **Authorization** header set to the token received in the `requestSchoolPasswordReset` mutation.    
Here is a sample mutation:    
#### Header
```
{
    "Authorization": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MzE4NjIwNzQsImlkIjoiMSIsInJvbGUiOiJzY2hvb2wifQ.K3rUjOd3rVnos1ShCOqAUqhXDILQGvJglZ5uoGwCC0k"
}
```
#### Body
```
mutation resetSchoolPassword{
  resetSchoolPassword(new_password:"Kh@1934"){
    id
    createdAt
    updatedAt
    deletedAt
    name
    phone_number
    password
    badge
    Website
  }
}     
```
Here is a sample response:     

```
{
  "data": {
    "resetSchoolPassword": {
      "id": 1,
      "createdAt": "2024-11-15T12:23:04.704399+03:00",
      "updatedAt": "2024-11-16T19:54:04.180065+03:00",
      "deletedAt": null,
      "name": "Kisii School",
      "phone_number": "+254123456789",
      "password": "$2a$14$5JVuR/JcFTL.zUKcVOCjgee5H5pN/i3QxVVQmSta9/Y0vxuq5car.",
      "badge": null,
      "Website": "www.kisiischool.ac.ke"
    }
  }
}
```

### refreshToken      
In the scenario that a user's token is about to expire but the user wants to stay logged in, the user can carry out a `refreshToken` mutation to renew the token. a `refreshToken` mutation takes the current `Token` as an input and returns a renewed one.    
Here is a sample mutation:      

```
mutation refreshToken {
  refreshToken(input:{Token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MzE4NjA1MjUsImlkIjoiMSIsInJvbGUiOiJzY2hvb2wifQ.RbrhUyos6vJVbljOLOLWFUEbSnDqExSYlGLjPhq5u68"})
}
```
Here is a sample response:      

```
{
  "data": {
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MzE4NjEwOTUsImlkIjoiMSIsInJvbGUiOiJzY2hvb2wifQ.PNdSAAm-j0CEuJiZO4Szd8nhln9wIhlYk5IajQEvWck"
  }
}
```


