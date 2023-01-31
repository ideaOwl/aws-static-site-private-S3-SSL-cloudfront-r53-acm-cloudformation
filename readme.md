## CloudFormation Static Website with *Private* S3 Bucket Template

Single template creates the infrastructure for a static website in AWS, including www to root from scratch in minutes. If you have static sites hosted elsewhere like MediaTemple, this is for you.

Features:
- SSL certificate for HTTPS with recommended TLSv1.2_2021
- S3 Bucket 
  - Private, only CloudFront accesses bucket contents
  - Single bucket for root and www domain access
- Single CloudFormation template means (almost*) complete rollback on deletion
- CloudFront
  - Origin Shield enabled
  - HTTP 2.0 enabled
  - IPv6 enabled
- Worldwide distribution

<sub>\* S3 bucket + contents skipped for deletion when rolling back template, seems like a good idea</sub>


Building parts:
- AWS S3
- AWS Certificate Manager (ACM) 
- AWS CloudFront
- AWS Route53
- AWS Cloudformation


## How to

- Apply `static-site-DOMAIN-r53-acm-s3-cloudfront.yaml` for the domain.
- Validate certificate by adding relevant `CNAME` to your current nameserver, or if new domain, add record to Route 53
- Upload your `index.html` to the S3 bucket

Your HTTPS-enabled website is provisioned.

## Notes

Credit to [sjevs'](https://github.com/sjevs/cloudformation-s3-static-website-with-cloudfront-and-route-53) original CloudFormation templates which were used to build this.  Noticed that there were old issues and pull requests (fixed in this), and I really didn't want the S3 buckets to be public so I took time to experiment with options.

A key difference here is that the S3 bucket isn't setup as a website: if it was and you try to connect CloudFront to the website URL, it couldn't be given access via OAC since only S3 origins are currently permitted that way.  The trade-off to a public S3 bucket then is 404s and subfolder redirects to index.html, but the latter is easily handled with adding [this singlular CloudFront function](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/example-function-add-index.html) and applied to all relevant static site CloudFront distributions.  I highly recommend staying away from the Lambda@Edge functions unless you need them, the CloudFront function was much simpler to implement and remove later.

To simplify things, this template does *not* work for subdomain s3 bucket creation, though the created root domain SSL cert already includes wildcards and so it should be simple to add another template later.

### Links

In order to apply the template you need to upload it to AWS CloudFormation: [How to upload the CloudFormation template](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-template.html)

