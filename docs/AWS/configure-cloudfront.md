# How to configure CloudFront + Ingress + NLB + S3 + WAF

Prior to cloudfront creation make sure you have the following:

An s3 bucket with public access read permission with static host configuration.
ACM public certificate from north virginia with the same domain name as the one of the ingress of the deployment. It means that there are two certificates with the same domain name xxx.yyy.com as main and \*.xxx.yyy.com

Create new distribution with the following attributes:

Configure the dns name Alternative domain name xxx.yyy.com and chose custom ssl certificate with the same domain name

For access to one page application in S3 configure “Default root object” to “ index.html “:

Create origin for S3 bucket with the public bucket that host the one page application:

Create following Origin request policy:

Create origin for elb:
Chose the load balancer for the intended deployment

Create new behavior for the load balancer and specify the path pattern to match to the rest API.
For example if you are using xxx.yyy.com/api/v1/xxx then it should be /api/\*

Chose the origin request policy to the one you have created previously and set the cache policy to managedCachingDisabled

Make sure that the behavior of the load balancer is with higher priority than the one of the s3 bucket:

After creation of cloudfront distribution:
In route53 Configure two DNS in A records in alias mode for xxx.yyy.com and \*.xxx.yyy.com with the value of the cloudfront DNS
The dns name of the cloudfront might not appear in the dropdown box so it has to be copy/paste to the route53 UI record field.

How to configure AWS WAF for a Cloudfront distribution:
In order to configure WAF for your cloudfront distribution, please follow these steps:
Open your aws console at “WAF and Shield”
Press the “Create web ACL” button

    Choose a name for your ACL and fill out the information forms
    Under the “Resource type” choose the “Cloudfront” option
    Press the “Add AWS resources” button











    Choose a relevant distribution from the list and press the “Add” button
    Press the “Next” button

Press the “Add rules button”

    In this example we will choose “Add managed rule groups” option and in the dropdown menu and choose among the preconfigured options. Should the need for a manual configuration of rules arise you can read up on the topic I the relevant sections of official AWS documentation:

https://docs.aws.amazon.com/waf/latest/developerguide/waf-rule-groups.html
https://docs.aws.amazon.com/waf/latest/developerguide/waf-rules.html

    In this tutorial we will choose rule groups from the “Free rule groups” section in order to not invoke any additional costs. Should the need arise you may freely apply anything from the paid or community sections.
    In order to add a rule group tick an option to the right of it. Keep in mind that a total number of rules you add must not exceed 1500. “Core rule set” contains rules that are generally applicable to web applications, while the rest were chosen as an example.
    Once all the applicable rules were chosen, press the “Add rules” button at the bottom of the screen.



    Press the “Next button”















    You can manage priority of your rules by ticking a relevant rule group and choose to heighten or lower it’s priority through the relevant buttons of the UI
    Once this is set up press the “Next” button



    If you’d like to receive metrics you can leave the following section unchanged and press the “Next” button. To disable the monitoring untick related rules


    Review your configuration and press the “Create ACL” button

In order to make sure everything is set up go to the Cloudfront section in your AWS console and open a relevant distribution.

Press the “Edit” button and make sure correct web ACL is attached

Should the need to edit your ACL arise go to the “Web ACL” section in WAF in aws console and press on the relevant ACL.

In this section you can track the monitoring data.

    In order to reconfigure rules choose the “Rules” tab and press the “Add rules button”
