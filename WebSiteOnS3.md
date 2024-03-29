# How to create a static web site that is hosted on S3 with support for HTTPs

Audience - those that need or want to set up static sites like the lobby and host them securely on S3 and are not AWS ninja's.

Note that AWS's services are flexible and customizable. There's a lot of different options. This documents how to set up a simple static site that supports https. A lot of the features of AWS's services were not used and I don'92t know how to use them, so in some cases I don't know why certain steps were taken; I just know that the documentation said to do it that way.

Assuming that we may have to go back and change things as we add more functionality, I have provided a brief explanation of why certain steps were taken, so that it isn't a mystery the next time.


Also note that there was some trial and error as I tried to customize the set-up and try different options. I left details of the errors at the bottom as they might come in handy.

# Overview

S3 is a service for hosting files on AWS. It also supports hosting static web sites but only provides http (non-encrypted) access to the sites. AWS provides other services that can be used in conjunction with S3 to:
- Provide https access (CloudFront + Certificate Manager + Route 53)
- Distribute copies of the web site so that the can be quickly fetched around the continent or world (CND) (CloudFront)
- Use a custom domain name instead of the AWS domain name (CloudFront + Route 53)

Details for setting up each service follow

# Pre-requisites

First get an AWS account (out of scope) 

For this example we used the following.

King Virtual Events - 0083 3495 0635
N. Virginia

AWS must provide the Domain Name Services for the domain (at least I think it does). Instructions for this are out of scope.

# Details

## Create S3 bucket

The following provide instructions on how to setup S3 web sites.
See https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/RoutingToS3Bucket.html
https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/RoutingToS3Bucket.html
https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html#root-domain-walkthrough-configure-bucket-permissions

These instructions show how to set up a publicly accessible bucket that has been configured to host a static web site in the file index.html. See the Trial and Error section for why this configuration is used.

Here's the take-away for a static web site.

1. Turn off the block public level access flag and clear all four options under the Block Public Access section. Note that this is a top level lock that prevents bucket contents from being made public, but
when turned off, buck content access still can be specified.
2. To create a static site, clink on the bucket's Properties
3. Click on the Static Web Site hosting box, it will open
4. Choose Use this bucket to host a static site
5. Specify index.html as the static file
6. Upload the index.html file 
7. Select the file and under overview, click Make Public

You should see a link and if you click on that link your browser will connect to the S3 bucket, download and display the index.html web page. If so, then the S3 bucket is set up.

Next step, provide HTTPs support.

## Create a CloudFront distribution

This serves two functions. First, it's a CDN that caches content closer to users to speed up response time. Second, it's how we can set up secure access to the site on AWS. We probably don't need much of the CDN functionality for the lobby site, so I choose the cheapest and default options for caching.


1. Select web delivery method (you will have to choices, web delivery or streaming (I think)
2. On the next screen, Set the Origin Domain name to the bucket
- Restrict Bucket Access - Select No (see what happened when I selected Yes i the Trial and Error section)
3. Redirect HTTP to HTTPs (which is more expensive than only allowing HTTPS)
4. Allow GET, HEAD access (maybe OPTIONS)
5. Distribution Price class - (US, Canada, and Europe only) cheapest 
6. Alternate Domain Names - if not using an AWS name, specify the name (e.g. testlobby.virtualvenue.ca). It might be that one is required to have AWS host the name server for this to work as one has to set up an alias A record. An alternate domain name is required in order to use a custom certificate.
7. Choose to use an AWS or custom TLS certificate. If you choose a custom certificate, you can have AWS create one (free). If you choose to create one, it sends you to the certificate manager - see below
8. When done come back to this page, wait for certificate to be validated, and the the certificate, will show up in the select box (at least it will show up once you start typing the cert name). Pick it.
9. select defaults for remaining options


## Route 53

### Route requests to Cloud Front

1. Create an alias record, a special type A record
  a. Need a CloudFront distribution that specifies that the domain (sub domain) is an alternate domain name
  b. The alias target will be the cloud front domain name (found under the general tab on the CloudFront distribution)




## Certificate Manager

Make sure that your domain is configured to use the AWD name servers or else the CNAME based steps for verifying that you own the domain will fail.

1. Select a domain name that you own. For this, I chose lobby.virtualvenue.ca
2. Use DNS validation, makes it easier if AWS Route 53 is used for name services. This validation step is used by AWS to confirm that you control (own) the domain. The assumption being, if you can change the domain records, you are in control of the domain. If you use AWS as you name server, it simplifies the process as it can change the domain records for you.
3. When you finish the request, you are sent to the Certificate Manager console from which
you can look at the request and click a button to have AWS update the DNS records so validation can complete ( leave those changes, CNAME records, in place so the certificate manager can continue to update certs )
4. Wait for AWS to finish validating the certificate creation request

If the certificate was being created as part of the configuration of a CloudFront distribution, then you can now go back to the CloudFront distribution and select the certificate.

# Status

S3 bucket exists and is public. Hosts the login page which likely doesn't work due to it's links.
TLS works and the page is accessible via lobby.virtualvenue.ca/index.html


# Trial and Error Notes

## S3

S3 can also service the content as a web service instead of a static web site. There were some instructions that suggested this as an option, when combined with locking the S3 bucket down so that it could not be accessed from the public, but only via CloudFront.

Locking the bucket down seemed like it could be useful for controlling access to parts of the site.

Attempts were made to both use S3 as a service and block access. They failed. But later, it turned out that the tests were screwed up because of an issue with both the Domain Name Service setup and the test URLs. I switched back to a public bucket that hosted a static site as I have been able to get that to work before.

One thing difference between the web service style bucket and the web site bucket is that one is more likely to see XML error messages.

I have experience with a public static web site. Although using S3 permissions to lock things down might have given us some options, it is OK to have the content in bucket be public - it's going to be accessed publicly via CLoudFront anyway.

## Cloud Front

In conjunction with the above private S3 bucket, I tried to configure Cloud Front so that it would
access the private bucket when serving content. To do so I tried the following on the first steps of the CloudFront Distribution setup:
1. Restrict Bucket Access - Picked Yes with a bucket that was not public, 
2. Create Origin Access Identity - either yes or use an existing - I think this plus the above make it so that CloudFront can access the bucket but the public cannot directly access it via S3
3. Grant Read Permissions on Bucket 
  - Given that the bucket was locked down above, then say yes, so cloud front will update the policies so this distribution can read the bucket, instead of us manually updating the S3 bucket policies

That didn't work (again likely do to the testing issues mentioned in the S3 section), so I made the bucket public and turned Restrict bucket access off. The other options (e.g. Grant Read Permissions, Create Origin Access Identity) were no longer visible (I also had to remove the policy that was create in the above attempt from the S3 bucket).


# TO DO 

More research on blocking public access 

Error pages for invalid URLs


User workflow - what do we display when the event is over?




