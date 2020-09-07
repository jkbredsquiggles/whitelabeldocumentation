
# Setting up Cognito

Log in
Select Cognito from the Services
Select Manage User Pools


## To Create a User pool
- pick a pool name, e.g. the event name 
- pick default
  - under attributes 
    - decide whether people can sign in with username or email or phone
    - decide whether people can use email or phone number as their user name
    - decide what information is required (just email?) (When we set up the initial pool the email field was selected as required, for Deighton we did not. Both seemed to work OK)
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
    - Using Cognito - do this for testing and dev as it is simpler to set up but has email quotas. To use Cognito, just select Cognito and don't both specifying
    the SES Region, From email address ARN, From email addess, or Reply-To email address fields
    - Using SES - used for production. It also has a sandbox mode while testing SES (as opposed to the above Cognito service for testing Cognito)
      - See https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-email.html for general instructions
      - The SES service must be set up for the domain and email addresses. See the section below for instructions. You will need to pick a from email address and reply to email address. The from email address is used for email transmission respones (e.g. if the email bounces SES or some intermediary can send a notice to from) and the reply to email address is the address that email clients will use if the recipient clicks reply (I think).
      - We chose info for the from address and noreply as the reply-to address
      - Specify the region in which SES was set up 
      - Specify the ARN of the from email address:
        - SES service -> Email Addresses -> select the address and click View details
        - Copy the value of the ARN field and paste it back in the appropriate field on the Cognito page
      - Specify the From and Reply to email address - I'm not quite sure why the from field is duplicated nor why the reply-to doesn't require an ARN but I think it is because
      from has a few meanings, first the verified account that is registered in SES for sending emails, second the from field in the actual email with additional things like the nice name (I suspect that emails must be the same with a possibly different nice name) and since the reply email address is not used to send emails it can be anything.
    - adjust the email templates - there is some flexibility but I’m not sure how much customization is allowed
    - click next
  - tags - just click next
  - triggers - Add and entry for the Custom Mssage trigger. Set it to the Lamdba that handles custom notices (customPOCRegistrationNotice). Click save.
  - do you want to remember user’s devices - I don’t know so I said no.
    - click next
  - app client - click next (set up app client later after the pool is created, see below)
  - custom workflows - accept default, will figure this out later
    - click next
  - review settings and create pool

## Create an app client
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

## Modify App Client Settings 

We apparently need to set callback URLS in order to use OAuth (which is required to integrate with our lambdas). We may figure out other option later.
- Select the Cognito User Pool as the identity provider
- pick call-back URL - where the login page sends you after successful login
- pick log out URL - the URL that the login service will provide when you logout?
- Allowed OAuthFlows - don’t know what this is, just followed the start-up guide instructions
  - Authorization code grant (I've since turned this off and things seem to work, both the demo pool (StaticCognitoPOC) and our first event have this off)
  - Implicit Grant
  - NOT client credentials
- Allowed OAuthScope calls - don’t know what this is, just followed the start-up guide instructions
  - all



### To use custom domain 

Set up a custom domain. Use the event name.



## User management

- Log in to AWS and go to Cognito
- Select General Settings.Users and groups
  - No need to bother with Groups at this point
  - Create User - pretty much self explanatory
  - Import Users
    - Click on download  csv header. It will download a file that is a template for adding users
    - edit the file in excel (or Numbers?) or a text editor. If editing in a spreadsheet, ensure that the file is saved as a csv. Note that there are some formatting rules, e.g. proper csv files require quotes around strings but aws does not want them. Also, if a name as a comma in it, put an escape backslash before it. For security reasons, you can't supply a password, the new user will be asked to pick a new password. A sample file that meets our current user pool criteria is provided below. The full rules for the csv file can be found at https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-using-import-tool-csv-header.html . A quick subset follows: 
      - cognito:username
      - cognito:mfa_enabled
      - email_verified 
      - phone_number_verified
      - email (if email_verified is true)
      - phone_number (if phone_number_verified is true)
      - Any attributes that you marked as required when you created the user pool 
      - One of email_verified or phone_verified must be true.
    - click create import job (this is the job management screen)
      - looks like you have to create a name , maybe each time
      - select or create a name for the user role that is required so that the Cognito import job can create log file entries and store them in CloudWatch. Cognito-UserImport-Role has already been created, so use it for now.
      - select the file to upload
      - click create job. You will be return to the job management screen. Select you job and click start. Jobs are asynchronous. The job will start, and the status will be set to Pending. Click refresh to view the update or go look a the logs. Logs will be generated and then at some point in the future the job status will be updated.
      - you can view the job history on CloudWatch 
        - go to the cloudwatch service. On the left panel, click logs, then click view log groups. The logs for cognito import should show up under /aws/cognito/userpools/poolid/poolname. At this point there is only one poolId and one pool name (which will likely be the event name in the future), so it should be easy to find the log group.
        - click on the log group. There will be one set of entries (log stream) for each import job (the last portion of the log stream name will match the job name you selected above). Click on the lock stream name. There should be one log entry for each user with information to indicate whether the user was imported OK or not. If all users failed, there is likely a problem with the format of the csv file (e.g. missing column). If a few users failed, the entries for those users can be copied to another file, corrected, and uploaded in a new job.
    - Users are not sent email notifications (at least not in the default import process). Users must use the forgot password process.


Sample csv upload:
```
cognito:username,email,email_verified,cognito:mfa_enabled,phone_number_verified,name,given_name,family_name,middle_name,nickname,preferred_username,profile,picture,website,gender,birthdate,zoneinfo,locale,phone_number,address,updated_at
address@example.ca,address@example.ca,true,false,false,,,,,,,,,,,,,,,,
address2@example.ca,address2@example.ca,true,false,false,,,,,,,,,,,,,,,,
```

Sample log file:
```
2020-06-17T13:14:04.300-04:00
Cognito User Pools Import - Test Log

2020-06-17T13:14:05.897-04:00
Cognito User Pools Import - Test Log

2020-06-17T13:14:06.093-04:00
[SUCCEEDED] Line Number 2 - The import succeeded.

2020-06-17T13:14:06.093-04:00
[SUCCEEDED] Line Number 3 - The import succeeded.
```

# Setting up SES

See https://docs.aws.amazon.com/ses/latest/DeveloperGuide/quick-start.html for general instructions on using SES

All the following steps are performed in the SES service. You will need to verify that you own the domain and verify details of your control over the domain to use various features. You will then verify the email accounts that you wish to use with SES and give AWS/SES the right to use those addresses

I set up SES in the same region as Cognito, I don't think that is required.

## Steps

### Verify the domain

This is to confirm with AWS that you have some control over the domain. Under SES-> Identity Management -> Domains, select verify a new domain. SES will walk you through the process, which is roughly:
- enter your domain name
- follow their instructions for modifying a TXT record on the domain - details depend on your DNS provider but they are intitive.
- once SES confirms that the TXT record has been modified (it could take a while), then it will indicate that the domain has been verified

#### DKIM

This is another verification section related to sending email that is further used to confirm that email has been sent from someone that controls the domain. It was listed as an option step, but I did it nonetheless. The process is similar to verifying the domain, but this time CNAME records are modified with your DNS provider. Again, once AWS/SES confirms that the CNAME records have been created, it will indicate that the DKIM is verified.

#### Custom Mail From Domain

At this point we don't use this.

### Verify email addresses

For each email address that is used by SES, verify the email address. To do so:
- go to SES-> Identity Management -> Email Addresses
- click Verify a New Email address
- enter the full email address (e.g. info@domain.com)
- AWS will send an email with a confirmation link to the address 
- when received click the provided link and follow the instructions to complete the verificaiton process

You should then be able to use the email on SES, in our case that means use the address for Cognito.



# Status

- Cognito is set up in N-Virginia region
- SES is set up in N-Virginia region




# Next Steps & Questions

- Research Amazon SES to undersatn customization / integration for emails

See https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-email.html
      
- Use SES to send a bulk signup message - the account is set up, we need to do some administrative stuff to switch out of sandbox mode
  


