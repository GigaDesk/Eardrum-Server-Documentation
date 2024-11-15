# Eardrum-Server-Documentation
This project provides a detailed documentation of eardrum-server API. Overally, eardrum-server is a school management system, that enable schools manage data that includes, teachers, students and other school entities. It leverages the following technologies:
* **GraphQL** : The project leverages the GraphQL data query and manipulation language for its API that allows a client to specify what data it needs.
* **OTP**    : The project leverages One-Time-Password for multi-factor authentication (MFA) process that requires the user to enter a code sent to their phone number alongside their account credentials.

## Endpoint
The API only includes a single endpoint

## Query Types
The API includes the following query types: 

### schoolPhoneNumberExists:
The `schoolPhoneNumberExists` query type executes following tasks:
* Checking if a school's phone number entry already exists
* Checking the state in which the phone number exists. In this case, whether it is verified or not.

A verified phone number is one that has been submitted by a school that went on to complete the multi-factor authentication (MFA) process, by entering the correct OTP 
sent to their phone number. On the other hand, an unverified phone number is one that school a school is yet to complete MFA process.

The `schoolPhoneNumberExists` query type takes the following arguments:
* `phone_number` : The specific phone number that one wishes to check its existence. The argument must follow the following rules:
  * It must be a `string` data type.
  * It cannot be omitted (non-nullable).
  * It must be in E.164 format

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
    createSchool( input: {name: "Kisii School", phone_number: "+254756142241", password:"Kisii@2024", Website:"www.kisiischool.ac.ke"} ) {    
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
      "phone_number": "+254756142241",
      "password": "$2a$14$0pT2rHYKopCGIdEO5.XeQOnspwBGgpbvoWRpdTBLzG5GwwmL910W6",
      "badge": null,
      "Website": "www.kisiischool.ac.ke"
    }
  }
}
```
***Note:*** After the mutation the user (a school in this case) should expect an OTP verification code sent to their phone number.
### sendCode
```
 mutation sendCode {   
    sendCode(phone_number: "+254756142241") {    
      phone_number
      success
     }    
 }
```
```
{
  "data": {
    "sendCode": {
      "phone_number": "+254756142241",
      "success": true
    }
  }
}
```
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
    verifySchool(input:{phone_number: "+254756142241", otp:"181794"}) {    
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
      "phone_number": "+254756142241",
      "password": "$2a$14$0pT2rHYKopCGIdEO5.XeQOnspwBGgpbvoWRpdTBLzG5GwwmL910W6",
      "badge": null,
      "Website": "www.kisiischool.ac.ke"
    }
  }
}
```
