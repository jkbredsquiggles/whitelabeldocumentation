# Operations Notes

Steps for updating site and supporting applications to support a new event

## Overview

- Deploy updated assets
- Create a Cognito Pool for the event, see the Cognito documention.
  - Keep track of the pool and app client ids as they are needed in followup steps
- Create Gateway API resource for the event that will point to the existing lambdas but use the correct Cognito pool to authenticate and authorize requests, see below
- Update the various applications' configurations
- Create an Amplify deploy that will point to the appropriate branch that has the version of the application for that event
- Deploy applications

## Configuration

Update configuration in :
- site’s config.
 - need config section for site (flesh out details later), but here’s the first items:
   - cognito pool id, region, and app client id in the auth section
   - gateway API urls in the protected resource section (after gateway API created, see below)
   - update assets urls to point to new custom assets 
- attendees lambda 
  - cognito pool id, region, and client app
  - full host name - used to validate requests
  - list of attendee attributes to pull from cognito (or whatever)
- custom authentication tool 
  - cognito pool id, region, and client app
  - least significant component of host name - this app keys the config lookup ONLY on pool Id as cognito can only provide that. host name is not used for validation logic, just to calculate tags for the email, e.g. the site url
  - email subject
- protectedresource
  - cognito pool id, region
  - full host name - used to validate requests
  - response data - currently just the playerId (used for both player and main stage chat)

## Create gateway API

### Add resource and methods for the event
- create resource at route of StaticPOCServices using the event name.
- in it:
   - create an attendees resource 
      - create a POST method, post to attendees lambda
        - Integration Type - Lambda function
        - Use Lambda proxy integration
        - pick region an lambda
      - for the method request, create an authorizer which points to the event’s cognito pool
        - edit the Post Method, click on Method Request and reference the event authorizer (see below)
      - accept defaults for other stuff
      - turn on CORS- click on the resource (POST), click on Actions drop down, select resource actions, enable CORS. Accept defaults
   - create a config resource
      - create a POST method, post to StaticPOCConfiguration lambda
        - Integration Type - Lambda function
        - Use Lambda proxy integration
        - pick region an lambda
      - for the method request, create an authorizer which points to the event’s cognito pool
        - edit the Post Method, click on Method Request and reference the event authorizer (see below)
      - accept defaults for other stuff
      - turn on CORS- click on the resource (POST), click on Actions drop down, select resource 
      
### Create gateway API authorizer
        - Under API Authorizers, create new Authorizer
          - Name = event name
          - Type = Cognito
          - specify the Cognito pool
          - token source = Authorization (from which header does the gateway get the token)

## Deploy Everything 

- commit site app to appropriate branch
- protectedResource to StaticPOCConfiguration - publish the update
- custom authentication tool to customerPOCRegistrationNotice - publish the update
- attendees to attendees - publish the update
- deploy the Gateway API to prod
