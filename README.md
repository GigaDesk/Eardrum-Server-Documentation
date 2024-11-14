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
* Checking if a phone_number entry already exists
* Checking the state in which the phone number exists. In this case, whether it is verified or not.

A verified phone number is one that has been submitted by a school that went on to complete the multi-factor authentication (MFA) process, by entering the correct OTP 
sent to their phone number. On the other hand, an unverified phone number is one that school a school is yet to complete MFA process.

The `schoolPhoneNumberExists` query type takes the following arguments:
* `phone_number` : The specific phone number that one wishes to check its existence. The argument must follow the following rules:
  * It must be a `string` data type.
  * It Cannot be omitted (non-nullable).
  * It must be in E.164 format, which includes the following components:
    * Plus Sign (+): This indicates an international number.
    * Country Code: A number that identifies the country. For example, the US country code is 1, the UK is 44, and Kenya is 254.
    * Area Code: A numerical code that identifies a specific geographic area within a country.
    * Local Number: The specific number assigned to a particular phone line.

An example is: "+254123456789"   
The `schoolPhoneNumberExists` return the type `PhoneNumberExists` that includes the following fields:  
* `verified` : `Boolean`. Shows `true` if verified.
* `unverified`: `Boolean`. Shows `true` if unverified.
Here is a sample query:

  query checkPhoneNumber {
    schoolPhoneNumberExists(phone_number: "+254756142241") {verifiedunverified}}

