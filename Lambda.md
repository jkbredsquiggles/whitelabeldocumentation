# Lambdas

AWS provides what they call a serverless service called Lambda. It is for hosting small event triggered applications:
- small memory footprint
- self contained except for access to network services, e.g. no need for stroring/reading files on a server
- complete request handling quickly

Lambda charges for the amount of CPU time and memory used to process a request and, for small applications that use little memory, millions of requests can be processed per month for pennies. They do not charge if the applications are not used - i.e. they host them for free when they are inactive but they are ready to handle requests on demand.

Lambda automatically scales the services, running them on multiple machines, as required, to meet demand.

We currently use Lamda for:
- a protected configuration application that will return configuration details for the appropriate event only to requests from users that are logged in to the event (protectedresource.js)
- customized emails for user authentication mananagement (customauthenticationmessage.js) - provides more flexibility than the basic Cognito emails, such as per event message content (which we are not using at this time).

The projected configuration application is publically exposed via the AWS API Gateway service. The customized email application is used internall (by Cognito) and does not require the API Gateway.


# Operations Notes

The source for our lambda applications are currently in the StaticCognitoPOC project - in the public/static/lambda folder.

Although they are part of the StaticCongnitoPOC project, they are not deployed to AWS via Amplify at this time (haven't had the time to figure out how to do it), so we deploy them manually.

Assuming that the application's API has not changed, i.e. i.e. there are no new or missing methods or different URLs or message or response types, then the API Gateway need not be modified as it points to the current production lambda. For now, API Gateway items are (or will be) documented with the project.

At this time that means:
- Log in to AWS, currently in the  us-east-1 N.Virgina region
- Go to the Lambda service
- You will see a list of the current Lambdas.
- Pick the appropriate lambda. That screen will display an editor with the application source code. Replace it with the new version and save it.
- At this point the lamdbda is not deployed and you can test it. There are various tests that are not decumented at this time.
- Once tested it can be deployed, click on Actions -> Publish New Version. You will be walked through the steps to deploy the new Lambda and start sending requests to it. Those steps involve providing a version name and brief description. Once complete, the new version will be active.
- AWS Lambda provides means to swap versions - I haven't played with it yet.



