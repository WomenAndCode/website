# Amazon Static Webpage: AWS S3 + AWS Cloudfront

I registered a new domain (womenandcode.org) and I want to use it to host a static website using S3 and Cloudfront. For this website I want an SSL connection using a AWS Certificate Manager certificate.

To finish this setup you have to go through these steps:

1. Create an AWS S3 bucket and upload your index.html file
2. Create a Cloudfront distribution pointing to this S3 bucket
3. Request a new SSL certificate in region us-east-1 (!)
4. Assign the certificate to your Cloudfront distribution

I assume that you already have a (new) domain registered with your DOMAIN PROVIDER OF CHOISE (e.g. inwx.de or Route 53) with no A records setup.


# 1) Create a new S3 bucket for your static files

Open the buckets properties and activate “Static website hosting”. Make note of the Endpoint URI:  http://www.womenandcode.org.s3-website.eu-central-1.amazonaws.com

Save the policy, upload your index.html file and your are done.
index.html is publicly available here: https://s3.eu-central-1.amazonaws.com/www.womenandcode.org/index.html


# 2) Create a cloudfront distribution using a custom CNAME

SSL certificates can only be assigned to Cloudfront distributions, so we need to create one to enable SSL for our static website.

In https://console.aws.amazon.com/cloudfront create a new Web distribution and set your endpoint URL (NOT your S3 bucket!!!) as "Origin Domain Name" 
endpoint: http://www.womenandcode.org.s3-website.eu-central-1.amazonaws.com

+ Select "HTTPS Only" for Viewer Protocol Policy.

In the Distribution Settings section enter your domain name you want to host your static files on ("Alternate Domain Name CNAMEs" = womenandcode.org and www.womenandcode.org). Do not change the SSL Certificate settings for now, as we did not setup our email address to receive the domain validation email for our certificate request.

Beside that keep all the default settings and click “Create distribution”. Grab a cup of coffee or two and wait until the distribution is created………


# 3) Request a free SSL certificate using AWS Certificate Manager (ACM)

Go to https://console.aws.amazon.com/acm/home?region=us-east-1#/

    Cloudfront only accepts certificates hosted in region us-east-1. Switch to that region!

Enter one or more domain names, you want to create a SSL certificate for. You can even use a wildcard:
-  womenandcode.org 
-  *.womenandcode.org 


Start Domain Verification:
- set the provided CNAME record at your DOMAIN PROVIDER OF CHOICE
- wait one minute or so to get verified


# 4) Assign the SSL certificate to your Cloudfront distribution

You’re almost done. Go back to Cloudfront and edit your distribution. Now you should be able to select your brand new SSL certificate.
Select a SSL certificate from AWS Certificate Manager


# 5) Set DNS Record

Verify: go to https://console.aws.amazon.com/cloudfront and check the Domain Name, e.g. https://d2p1qt5bybmalu.cloudfront.net/
It should show the content of your index.html


Set CNAME record at your DOMAIN PROVIDER OF CHOICE*:
womenandcode.org --> CNAME --> d2p1qt5bybmalu.cloudfront.net


ALT!!! DNS settings:
[blank] URL http://www.womenandcode.org.s3-website.eu-central-1.amazonaws.com
www     URL http://www.womenandcode.org.s3-website.eu-central-1.amazonaws.com

title: Women&&Code
desc: A tech initiative for women in Vienna. JavaScript learning groups and other events, workshops, and hackathons
keywords: JavaScript,courses,web development,tutorials,programming,training,learning,lessons,learning group,feminist,women,coding,beginners,study group
favicon: http://womenandcode.org/favicon.ico



Congratulations. You’re done :-) Check it out: https://womenandcode.org




# Google Firebase & Cloudflare Static Webpage

## Storage

upload index.html
-> https://firebasestorage.googleapis.com/v0/b/womenandcode.appspot.com/o/index.html

## Rules

    // Anyone can read or write to the bucket, even non-users of your app.
    // Because it is shared with Google App Engine, this will also make
    // files uploaded via GAE public.
    service firebase.storage {
      match /b/{bucket}/o {
        match /{allPaths=**} {
          allow read, write;
        }
      }
    }


+ cloudflare.com