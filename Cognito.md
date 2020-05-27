
Log in
Select Cognito from the Services
Select Manage User Pools


To Create a User pool
- pick a pool name, e.g. the event name 
- pick default
  - under attributes 
    - decide whether people can sign in with username or email or phone
    - decide whether people can use email or phone number as their user name
    - decide what information is required (just email?)
    - click next step
  - signup workflow
    - password strength - choose defaults
    - allow them to sign themselves up (probably not)
    - initial password expiry - accept default ?
    - clicks next step
  - signup and password recovery
    - MFA - probably not
    - how can user recover account - email only for events
    - only verify email
    - don’t provide a role for SMS messages (unless you want it)
    - click next step
  - email and  SMS configuration
    . will probably want to use Amazon SES but to do so we’ll need to configure your domain. I used cognito, which means the emails have amazon addresses
    - adjust the email templates - there is some flexibility but I’m not sure how much customization is allowed
    - click next
  - tags - just click next
  - do you want to remember user’s devices - I don’t know so I said no.
    - click next
  - app client - click next (set up app later)
  - custom workflows - accept default, will figure this out later
    - click next
  - review settings and create pool

Create an app client
  - not sure exactly what this means, but it roughly means to set up the server so that a given web (or mobile) app can use it.
  - pick a name
  - cognito will generate an id, copy it somewhere safe
  - turn off app client secret (that’s used by web side apps)
  - Accept the default auth flow configuration
    - Enable lambda trigger based custom authentication (ALLOW_CUSTOM_AUTH)
    - Enable SRP (secure remote password) protocol based authentication (ALLOW_USER_SRP_AUTH)
    - Enable refresh token based authentication (ALLOW_REFRESH_TOKEN_AUTH)
    - Prevent User Existence Errors - Enabled
    - save 

Modify App Client Settings - we might not use this unless we redirect users to the AWS login page instead of embedding it in your web page - to discuss with LightSpeed
- pick call-back URL - where the login page sends you after successful login
- pick log out URL - the URL that the login service will provide when you logout?
- Allowed OAuthFlows - don’t know what this is, just followed the start-up guide instructions
  - Authorization code grant
  - Implicit Grant
  - NOT client credentials
- Allowed OAuthScope calls - don’t know what this is, just followed the start-up guide instructions
  - all

Click Launch Hosted UI to be sent to the login page


To use custom domain - will do this later

- Go to App integration.Domain nam
  - Select Use your domain - will need  a certificate from AWS and need to update domain alias


User management

- Log in to AWS and go to Cognito
- Select General Settings.Users and groups
  - No need to bother with Groups at this point
  - Create User - pretty much self explanatory
  - Import Users
    - Click on download  csv header. It will download a file that is a template for adding users
    - edit the file in excel, I’m pretty sure you can leave many of the fields blank, for the above configuration I think only email is required (maybe with a bunch of trailing commas). We’ll have to test.
    - click create import job
      - looks like you have to create a name , maybe each time
      - not sure if you will have to create  a role each time or not - I will look into this later
      - upload the file.


      

  


