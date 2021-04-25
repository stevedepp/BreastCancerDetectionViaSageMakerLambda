# Breast Cancer Detection Via SageMaker & Lambda in 84 steps      
Another TGIF project     

Please click the video to hear sound or follow along with the slides and transcript below the video.

![demo](https://user-images.githubusercontent.com/38410965/111722556-722c8b00-8838-11eb-8ab8-fd7caa2d006e.mp4)

#

> Hi. This is Steve.  I’m going to present the 84 steps from Rumi Olsen’s blog where she demo’s SageMaker that predicts breast cancer by calling on a SageMaker model’s endpoints, uses Lambda and the API Gateway, tests that API using Postman app.   I’m running on a Mac; so, there are a few complications that I deal with along the way.  There are 84 slides; so, I’ll get on with this.  Thank you very much for watching.

### Steve Depp     
### DV8 - breast cancer prediction in only 84 clicks     

AWS SageMaker model endpoints called using Amazon API Gateway and AWS Lambda     
from this demo blog by Rumi Olsen of Amazon     
https://aws.amazon.com/blogs/machine-learning/call-an-amazon-sagemaker-model-endpoint-using-amazon-api-gateway-and-aws-lambda/     

**Need 6 things**     
- IAM roles = steps 1a … 1e     
- S3 bucket = steps 2a … 2b     
- AWS SageMaker = steps 3a … 3z     
- AWS Lambda = steps 4a … 4q     
- Amazon API Gateway = steps 5a … 5q     
- Postman = steps 6a … 6q     

**Do them in 5 + 2 + 17 + 26 + 17 + 17 = 84 steps.**     

<img width="1222" alt="AWS Management Console" src="https://user-images.githubusercontent.com/38410965/115980335-05ed2780-a55a-11eb-8a8b-b4404cce8f8c.png">

#

> OK. So, we start with IAM roles that are used later to allow Lambda to execute SageMaker endpoints for us.  

### 1a
### IAM roles     

create role      
https://console.aws.amazon.com/iam/home?region=us-east-1#/roles     

<img width="1132" alt="httpsconsole aws amazon comiamhomeregion=us-east-1#" src="https://user-images.githubusercontent.com/38410965/115980379-46e53c00-a55a-11eb-91d7-0fdf78b13286.png">

#

> First, we create a role.  Then, we select Lambda to enable Lambda to call AWS services for us.  

### 1b     
### IAM roles      
select “Lambda” to enable Lambda to call AWS services     
specifically SageMaker     

<img width="1222" alt="Choose a use case" src="https://user-images.githubusercontent.com/38410965/115980389-5b293900-a55a-11eb-86a5-5c996bd8ed2b.png">

#

> We pick a policy.  

### 1c     
### IAM roles     
pick a policy      
select “AmazonSageMakerFullAccess”     

<img width="1222" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980396-6b411880-a55a-11eb-8b97-dc5e92605889.png">

#

> We give the policy a name.  [ I meant to say give the role a name. ]

### 1d     
### IAM roles     
give it a name      
Role name = “lambda-full-sagemaker”     
tap “Create role”     

<img width="1222" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980405-7e53e880-a55a-11eb-9467-9861ed55020d.png">

#

> And we are done with that. 

### 1e     
### IAM roles     
done      

<img width="1222" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980417-9a578a00-a55a-11eb-8de4-75a3320e7439.png">

#

> We then, … We are going to need a bucket to hold all of our data and the model.  So, we create a bucket.   

### 2a     
### Amazon S3      
Create bucket      

<img width="1132" alt="Services" src="https://user-images.githubusercontent.com/38410965/115980425-a8a5a600-a55a-11eb-94c1-c4c5436579f9.png">

#

> We give it a name, and we’re done. 

### 2b     
### Amazon S3      
give it a name      
“deep-breast-cancer-bucket”     

<img width="1132" alt="Create bucket" src="https://user-images.githubusercontent.com/38410965/115980438-b8bd8580-a55a-11eb-977f-09391691af78.png">

<img width="742" alt="© Successfully created bucket depp-breast-cancer-bucket" src="https://user-images.githubusercontent.com/38410965/115980439-bce9a300-a55a-11eb-9e0e-edb58e6f83ed.png">

#

> OK, onto the meat.  I won’t spend a lot of time on SageMaker notebooks per se, but just touch on some of the salient points related to this exercise.  I’ll select Notebook instances here on this page.  

### 3a     
### Amazon SageMaker     
https://console.aws.amazon.com/sagemaker/home?region=us-east-1#/dashboard     
select “Notebook instances”      

<img width="1222" alt="Amazon SageMaker X" src="https://user-images.githubusercontent.com/38410965/115980448-d0950980-a55a-11eb-94bb-c1e17b155fee.png">

#

> I’ll create a notebook instance. 

### 3b     
### Amazon SageMaker     
select “Create notebook instance”      

<img width="1222" alt="Amazon SageMaker X" src="https://user-images.githubusercontent.com/38410965/115980456-e1de1600-a55a-11eb-941b-4d132183e60e.png">

#

> Give the notebook instance a name.      

### 3c     
### Amazon SageMaker     
give the notebook instance a name      
“breast-cancer-notebook”     

<img width="1132" alt="Create notebook instance" src="https://user-images.githubusercontent.com/38410965/115980466-f1f5f580-a55a-11eb-8b96-29b42e696b21.png">

#

> Scroll down.  This is where it got a little dicey for me.  “Create a new role” is what I chose ...      

### 3d     
### Amazon SageMaker     
then scroll down      
“Choose a role or let us create a role with the AmazonSageMakerFullAccess IAM policy attached”     
(I didn’t see that policy anywhere in the next slides.)     
Create a new role     

<img width="800" alt="Permissions and encryption" src="https://user-images.githubusercontent.com/38410965/115980472-00dca800-a55b-11eb-8531-41cd65bff68f.png">

#

> ... which takes you to this page for which I’m not really sure whether this is creating a role that is spoke about in the fine print.  

### 3e     
### Amazon SageMaker     
takes you here     
is this it?     
anyway, hit “Create role”      

<img width="1132" alt="Create an IAM role" src="https://user-images.githubusercontent.com/38410965/115980480-118d1e00-a55b-11eb-9c55-f02d74c86a45.png">

#

> OK and then that’s done. 

### 3f     
### Amazon SageMaker     
I would rather create a more generic role     
hit “Create Notebook Instance” 

<img width="803" alt="Permissions and encryption" src="https://user-images.githubusercontent.com/38410965/115980485-2073d080-a55b-11eb-8b58-0f5438d32091.png">

#

> Notebook is being created.       

### 3g     
### Amazon SageMaker     
notebook bring created     

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980496-2ec1ec80-a55b-11eb-99d4-1a2b9c499322.png">

#

> In about 3 minutes, you can tap on “Open Jupyter” ...

### 3h     
### Amazon SageMaker     
after 3 minutes ...     
tap “Open Jupyter”      

<img width="662" alt="Notebook instances" src="https://user-images.githubusercontent.com/38410965/115980501-3ed9cc00-a55b-11eb-8210-a1a070cec98a.png">

#

> ... which brings you to the Jupyter notebook that everybody knows.  Tap on “SageMaker Examples”.

### 3i     
### Amazon SageMaker     
you are here ...     
tap “SageMaker Examples”      

<img width="1132" alt="jupyter" src="https://user-images.githubusercontent.com/38410965/115980507-4dc07e80-a55b-11eb-9c80-db7c18c8de46.png">

#

> Open up “Introduction to Applying Machine Learning”, then “Breast Cancer Prediction” notebook. Then tap on “Use”.

### 3j     
### Amazon SageMaker     
open up “Introduction to Applying Machine Learning”      
then —> “Breast Cancer Prediction.ipynb”      
then —> tap “Use” 

<img width="1132" alt="jupyter" src="https://user-images.githubusercontent.com/38410965/115980517-5dd85e00-a55b-11eb-8520-10049bb43a8d.png">

#

> Create a copy.     

### 3k     
### Amazon SageMaker     
Create copy      

<img width="1132" alt="jupyter" src="https://user-images.githubusercontent.com/38410965/115980529-6b8de380-a55b-11eb-90f6-8074ff4ce3ad.png">

#

> And now we have a copy.  You can use this, and it will actually be saved into our SageMaker instances file. 

### 3l     
### Amazon SageMaker     
you have a copy      
“Breast Cancer Prediction”     

<img width="1132" alt="Breast Cancer Prediction" src="https://user-images.githubusercontent.com/38410965/115980534-78aad280-a55b-11eb-9103-c9f9c9ae9694.png">

#

> The important thing is at the very bottom, you need to go down and comment out this line so you don’t lose your endpoint at the end of the run

### 3m     
### Amazon SageMaker     
IMPORTANT      
skip to the bottom and comment this line beforehand so you don’t loose your endpoint      

<img width="916" alt="Add tag" src="https://user-images.githubusercontent.com/38410965/115980545-86605800-a55b-11eb-91fb-db91ac982124.png">

#

> Back at the top, we have some dependencies including the Amazon bucket that we created.  

### 3n     
### Amazon SageMaker     
back up top      
some dependencies      
including Amazon S3 bucket:     

"depp-breast-cancer-bucket” —> “<your_s3_bucket_name_here>”

<img width="1132" alt="Admin •" src="https://user-images.githubusercontent.com/38410965/115980550-937d4700-a55b-11eb-9ba8-b8ccd4cdd34e.png">

#

> Get the data. 

### 3o     
### Amazon SageMaker     
get the data      

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980557-a132cc80-a55b-11eb-8fd9-bce8ebd0ca75.png">

#

> This is some output here. The most important thing is to know is that “B” stands for benign.  And we will see that at the end of the presentation. 

### 3p     
### Amazon SageMaker     
some output where      
“B” = benign      
we see that again in 60 slides ...     

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980566-b3146f80-a55b-11eb-9b25-2e6a3600ce90.png">

#

> Create the sets.  

### 3q     
### Amazon SageMaker     
create the sets      
convert to recordIO-wrapped protobuf format = SageMaker’s favorite flavor     

<img width="1132" alt="Admin •" src="https://user-images.githubusercontent.com/38410965/115980572-c0c9f500-a55b-11eb-8496-d5e24840c495.png">

#

> Set up and train the validate objects.  Sorry.  Set up the train and validate objects. 

### 3r     
### Amazon SageMaker     
set up train and validate objects      

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980580-cd4e4d80-a55b-11eb-9042-959cf6460cea.png">

#

### 3s     
### Amazon SageMaker     
container images needed     

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980585-d93a0f80-a55b-11eb-8cf8-ae92cb18382e.png">

#

### 3t     
### Amazon SageMaker     
Job name is: DEMO-linear-2020-05-29-14-05-07     

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980589-e8b95880-a55b-11eb-93c1-b8f5342144c4.png">

#

> Training takes about 4 minutes each time.  So, it’s pretty consistent.   

### 3u     
### Amazon SageMaker     
training * wall time = 4 min     

#

> Our hosted model will be in this ARN.  Now, I didn’t know what an ARN was.  

### 3v     
### Amazon SageMaker     
our hosted model will be in this ARN      
arn:aws:sagemaker:us-east-1:606363841935:model/demo-linear-2020-05-29-14-05-07     

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker an" src="https://user-images.githubusercontent.com/38410965/115980599-fec71900-a55b-11eb-86f8-b2092c71d253.png">

#

> So, I looked it up.  It’s an Amazon Resource Name. 

### 3w     
### Amazon SageMaker     
you mean you didn’t know what an ARN is?     
arn is Amazon Resource Name      

#

> So, this is the important part.  You configure the endpoint. 

### 3x     
### Amazon SageMaker     
configure endpoint      

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980611-14d4d980-a55c-11eb-97fc-b9cfcc911041.png">

#

> And then we create the endpoint. Now, if you do this over and over and over again, the endpoint is named with a timestamp on it and you’re only allowed two endpoints.  Maybe that’s just for the free service, but you’re only allowed two. So, you have to go back and delete them if you run this over and over again. 

### 3y     
### Amazon SageMaker     
create endpoint      
wall time = 8 minutes every time     

<img width="1132" alt="Admin •" src="https://user-images.githubusercontent.com/38410965/115980620-27e7a980-a55c-11eb-9919-add2fa1fa2f9.png">

#

> Call the endpoint to make predictions.      

### 3z     
### Amazon SageMaker     
call endpoint to make predictions      
convert files to CSV     
	protobuf to train     
	CSV to predict?     

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker aw" src="https://user-images.githubusercontent.com/38410965/115980630-346c0200-a55c-11eb-9e8b-e97ac9f56b74.png">

#

> So, now with Lambda, we’re going to let Lambda call SageMaker for us.  We need to set up the Lambda to begin with.  So, “Create function”. 

### 4a     
### AWS Lambda     
https://console.aws.amazon.com/lambda/home?region=us-east-1#/discover     
Create function      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980639-42ba1e00-a55c-11eb-8e45-3c9c009f91fc.png">

#

> Give it a name. 

### 4b     
### AWS Lambda     
Author from scratch     
give it a name      
Function name = ‘breastcancer-lambda”     

<img width="1132" alt="_ console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980657-62514680-a55c-11eb-9ec7-5046d0cc1df0.png">

#

> Give Lambda the IAM execution role.  Now, this is really where I lost it.  Anyway.  Back here is says “IAM execution role that includes the following policy” and then it offers to do that for you, but I just cannot figure out where and how you set up a very specific policy like this. So, … 

### 4c     
### AWS Lambda     
give Lambda an IAM execution role ...     
	hair pull / yell at the kids / @#$%&*!!     
	—>  I am (IAM) unsure how to set up a specific      
		“IAM execution role that includes the following policy”      
		and I question: why not just show us?  The screenshots are free!     
... here is that idiotic quote from the demo here:     
https://aws.amazon.com/blogs/machine-learning/call-an-amazon-sagemaker-model-endpoint-using-amazon-api-gateway-and-aws-lambda/     

<img width="741" alt="Contact Sales support" src="https://user-images.githubusercontent.com/38410965/115980655-5c5b6580-a55c-11eb-8105-dce2f76c2c47.png">

#

> Give Lambda the IAM execution role.  Now, this is really where I lost it.  Anyway.  Back here is says “IAM execution role that includes the following policy” and then it offers to do that for you, but I just cannot figure out where and how you set up a very specific policy like this. So, … 

#

> ... I just gave my policy the world, basically everything to do with SageMaker.

### 4d          
### AWS Lambda     
as a noob, I give my model an IAM role with all sorts of superpowers      
Choose or create an execution role —> Use an existing role —> “lambda-full-sagemaker” created in step 1     
Create function     

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115981414-882d1a00-a561-11eb-9b32-ced4e5207ff5.png">

#

> So, that policy is set up, and the Lambda function is set up. 

### 4e     
### AWS Lambda     
green means go, but wait don’t order yet!     

<img width="1132" alt="breastcancer-lambda" src="https://user-images.githubusercontent.com/38410965/115980677-92004e80-a55c-11eb-9e02-9eefc6db6ef5.png">

#

> Scroll down and give Lambda the code that is from Rumi’s demo blog. So, I just cut and pasted that here.  

### 4f     
### AWS Lambda     
scroll down and give Lambda the code from Rumi’s demo blog * here:     
https://aws.amazon.com/blogs/machine-learning/call-an-amazon-sagemaker-model-endpoint-using-amazon-api-gateway-and-aws-lambda/     

<img width="1222" alt="Admin 462" src="https://user-images.githubusercontent.com/38410965/115980684-9f1d3d80-a55c-11eb-8190-9f5c376f0271.png">

#

> That’s the old code that was in there.  

### 4g     
### AWS Lambda     
out with the old ...      

<img width="1132" alt="breastcancer-lambda" src="https://user-images.githubusercontent.com/38410965/115980693-ae9c8680-a55c-11eb-9ab8-ee26947caebe.png">

#

> Here’s the new code. 

### 4h     
### AWS Lambda     
... in with the new     

<img width="1132" alt="breastcancer-lambda" src="https://user-images.githubusercontent.com/38410965/115980707-beb46600-a55c-11eb-9acd-a5a5aa77283a.png">

#

> OK. So, the code has in it an endpoint name variable and that has to be assigned to an environment variable. So, you need to go back and get that …

### 4i     
### AWS Lambda     
wait … still more to do     
isn’t this easy?     
this part of the code needs to know the ENDPOINT_NAME as environment variable      
where’s that name?     

<img width="624" alt="image" src="https://user-images.githubusercontent.com/38410965/115980731-eacfe700-a55c-11eb-8cd5-69c8e52d64bc.png">

go back and get the endpoint name from SageMaker      
https://console.aws.amazon.com/sagemaker/home?region=us-east-1#/dashboard     

<img width="1132" alt="Amazon SageMaker" src="https://user-images.githubusercontent.com/38410965/115980735-f0c5c800-a55c-11eb-991b-f774882ed8ad.png">

#

> … from SageMaker.  So, you go down to endpoints on the left hand side menu. 

### 4j     
### AWS Lambda     
scroll down, on the LHS select, see “Endpoint configurations” and “Endpoints”.       
We set up a configuration and the endpoint earlier * and can now go here and see them.     

<img width="1132" alt="Amazon SageMaker" src="https://user-images.githubusercontent.com/38410965/115980744-00451100-a55d-11eb-80a2-48906578657e.png">

#

> Click on these things … 

### 4k     
### AWS Lambda     
lets look at it to find a name      

<img width="1132" alt="_console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980751-0d620000-a55d-11eb-9454-1621c19b017a.png">

#

> … looking for a name.  And there’s the name:  “DEMO-linear-endpoint” and then it has a timestamp at the end.  That’s the thing I said that you only get really two of.  So, if you name it with a time stamp, then you really run out very quickly.

### 4l     
### AWS Lambda     
ok that’s enough ...     
the name is      
	“DEMO-linear-endpoint-202005291419”      

<img width="1132" alt="DEMO-linear-endpoint-202005291419" src="https://user-images.githubusercontent.com/38410965/115980758-1c48b280-a55d-11eb-9e47-6fe6fd9df8e8.png">

#

> And that matches the endpoint that was part of the Jupyter notebook output in one of the cells.  So, you could always go there as well. 

### 4m     
### AWS Lambda     
that matches the endpoint configuration and endpoint creation in our Jupyter notebook      

<img width="1132" alt="breast-cancer-notebook notebook us-east-1 sagemaker av" src="https://user-images.githubusercontent.com/38410965/115980768-2c609200-a55d-11eb-88d9-0c016e1fe52b.png">

#

> Back to the Lambda page, and you scroll down to where you put that in. Click on “Manage environment variables”.

### 4n     
### AWS Lambda     
https://console.aws.amazon.com/lambda/home?region=us-east-1#/functions/breastcancer-lambda?newFunction=true&tab=configuration     
back to lambda page and scroll down * to ...     
Environment Variables —> Manage Environment Variables      

<img width="1132" alt="breastcancer-lambda" src="https://user-images.githubusercontent.com/38410965/115980773-3bdfdb00-a55d-11eb-8d8d-422b2d5a7f02.png">

#

> Put “ENDPOINT_NAME” and the name of the endpoint in, and hit “Save”.

### 4o     
### AWS Lambda     
brings you to this screen ...     
Add Environment variables      
—> enter “ENDPOINT_NAME”     
—> paste the endpoint name “DEMO-linear-endpoint-202005291419”     
(don’t include the quotation marks)     
hit Save      

<img width="1132" alt="variables" src="https://user-images.githubusercontent.com/38410965/115980780-47cb9d00-a55d-11eb-98dd-887206fbd55e.png">

#

> Hit “Save” again.      

### 4p     
### AWS Lambda          
Your changes have been saved (uh ok)     
hit Save again ...     

<img width="1132" alt="breastcancer-lambda" src="https://user-images.githubusercontent.com/38410965/115980787-59ad4000-a55d-11eb-9823-ed511863bd92.png">

# 

> “Successfully updated the function breastcancer-lambda“.  And you’ll have to remember that name because when you use it later on in the third step. 

### 4q     
### AWS Lambda     
successfully updated the function breastcancer-lambda      
let’s remember that name for the next 3rd step: “breastcancer-lambda”      

<img width="1132" alt="breastcancer-lambda" src="https://user-images.githubusercontent.com/38410965/115980792-6c277980-a55d-11eb-9103-48d2de7d84a4.png">

#

> Alright. So, here we set up the API to distribute the powers we’ve given to Lambda, err, to distribute these powers that Lambda’s borrowed from SageMaker, rather.  So, just tap on “Get Started”   

### 5a     
### Amazon API Gateway     
https://console.aws.amazon.com/apigateway/home?region=us-east-1#/welcome     
this is straightforward (not)     
tap “Get Started”      

<img width="1132" alt="Amazon API Gateway" src="https://user-images.githubusercontent.com/38410965/115980801-7d708600-a55d-11eb-9987-7643385932dc.png">

#

> It says in this message hit “Import”, but don’t do that. 

### 5b     
### Amazon API Gateway     
regardless what this says ...     
don’t select Import      
jeez ...     
click OK      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980804-895c4800-a55d-11eb-85eb-92ac0f054cc5.png">

#

> Just select “New API” from the radio button, and then “Create New API”.  

### 5c     
### Amazon API Gateway     
select “New API” * radio button in the “Create new API” section      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980812-9711cd80-a55d-11eb-9b06-051b5963897b.png">

#

> We have to give the API a name, and hit “Create API”. 

### 5d     
### Amazon API Gateway     
give it a name      
“BreastCancerPrediction” (without quotation marks)     
hit Create API      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980826-ab55ca80-a55d-11eb-8e49-2fcc19768b2f.png">

#

> Pull down from the “Actions” menu to “Create a resource”.     

### 5e     
### Amazon API Gateway     
pull down menu “Actions” to “Create Resource”     

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980833-b9a3e680-a55d-11eb-9d59-10f3e2b38ec6.png">

#

> Give the resource a name, and hit “Create resource”.

### 5f     
### Amazon API Gateway     
give the resource a name      
Resource name = predictbreastcancer     
hit Create Resource      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980845-c7f20280-a55d-11eb-8f21-b2b82dfcfc22.png">

#

> Pull down from the “Actions” pulldown menu again.  “Create Method”

### 5g     
### Amazon API Gateway     
from Actions pulldown menu ...     
Create Method      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980850-d6401e80-a55d-11eb-961e-4d2730812320.png">

#

> Select “POST”.  And then, I didn’t really, you know, know what to do here, but I figured it out.  You just click on the checkmark and move on.

### 5h     
### Amazon API Gateway     
from the pull down menu below “predictbreastcancer”      
select POST and  (obviously) click the checkmark to move on      

<img width="645" alt="No methods defined for the resource" src="https://user-images.githubusercontent.com/38410965/115980859-e526d100-a55d-11eb-8cc8-a1bdb0da77ef.png">

<img width="194" alt="predictbreastcancer" src="https://user-images.githubusercontent.com/38410965/115980865-e9eb8500-a55d-11eb-9461-97085bd72b0f.png">


#

> If you give it the Lambda function name, and hit “Save”. 

### 5i     
### Amazon API Gateway     
here’s where we use that name we saved for lambda function      
give it the lambda function to use “breastcancer-lambda”      
hit Save      

<img width="1132" alt="_ console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980885-08ea1700-a55e-11eb-89fb-186b0ac85f39.png">

#

> And this is a little warning.      

### 5j     
### Amazon API Gateway     
warning ...     
60 steps later… am I sure?     

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980889-10a9bb80-a55e-11eb-98ea-476757e0d7a2.png">

#

> This is actually a very good diagram of how the information’s going to flow from client and back to client.  

### 5k     
### Amazon API Gateway     
great diagram      
shows the flow of information from client to client     
lets hope we get status 200!     

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980896-1e5f4100-a55e-11eb-87b7-5352fb81e1a0.png">

#     

> You hit “Deploy”.     

### 5l     
### Amazon API Gateway     
from actions, pull down (again) to API ACTIONS      
—> Deploy      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980904-2d45f380-a55e-11eb-989e-cf0a645b8ba0.png">

#

> You need to give the API a name.  I chose “test”.

### 5m     
### Amazon API Gateway     
pull down from “Deployment stage” to [New Stage]     
give it a name like ‘test’      

<img width="186" alt="New Stage" src="https://user-images.githubusercontent.com/38410965/115980909-3a62e280-a55e-11eb-8c26-f4321b4dc49b.png">
￼
hit Deploy      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980910-3fc02d00-a55e-11eb-8b24-ee43376e4004.png">

#

> And we are here. So, it has a URL, but that is not the URL you want to use.  There’s “test” over there to the left. 

### 5n     
### Amazon API Gateway     
you are here now      
there’s a URL      
is that the one?      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980923-4ea6df80-a55e-11eb-8947-ee8d50a944d5.png">

#

> What you want to do is to click on “test” and then click on “POST” below that.  And that will be your URL.  

### 5o     
### Amazon API Gateway     
missing in this demo site      
https://aws.amazon.com/blogs/machine-learning/call-an-amazon-sagemaker-model-endpoint-using-amazon-api-gateway-and-aws-lambda/
are the following instructions      
to get your URL, don’t look on the last page      
you need to:      
click on ‘test’      
tap the name you gave your API      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980933-5c5c6500-a55e-11eb-8efd-e2ad6bebbfe6.png">

#

> 

### 5p     
### Amazon API Gateway     
then POST      
this is your URL      

<img width="1132" alt="console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980946-6b431780-a55e-11eb-8333-0896ba3f9c47.png">

#

> And this is the difference between the two.  It really just lacks a couple things on the very end.   

### 5q     
### Amazon API Gateway     
notice the differences      

￼<img width="818" alt="test Stage Editor" src="https://user-images.githubusercontent.com/38410965/115980957-7ac26080-a55e-11eb-84b9-e31eee517a0b.png">

￼<img width="833" alt="test - POST - predictbreastcancer" src="https://user-images.githubusercontent.com/38410965/115980964-801fab00-a55e-11eb-90f5-8e13861421a3.png">

lets save that for the next step https://nqk1vty9m6.execute-api.us-east-1.amazonaws.com/test/predictbreastcancer

#

> So, now in the final step, we’re going to use the Postman app to call the API, and we don’t have to use authentication because we’re using this app. So, that makes it easier. 

### 6a     
### Postman     
https://www.postman.com     
get it      

<img width="1132" alt="The" src="https://user-images.githubusercontent.com/38410965/115980969-9168b780-a55e-11eb-8bce-f0671638228e.png">

#

> So, we have to get some data to test it with.  If you’re on a Windows machine, this works out really well. You’re just going to go back to ...      

### 6b     
### Postman     
get some data to test it with      
if you are on Windows     
go back to your bucket for a sec     
https://s3.console.aws.amazon.com/s3/home?region=us-east-1     
click on “depp-breast-cancer-bucket”     

<img width="1132" alt="@s3 console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980976-9e85a680-a55e-11eb-87a2-84e4662350c3.png">

#

> ... SageMaker, ...        

### 6c     
### Postman     
click on sagemaker      

<img width="1132" alt="s3 console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980986-a9d8d200-a55e-11eb-98da-567ba12c109d.png">

#

> ... click on ...      

### 6d     
### Postman     
click on DEMO-breast-cancer-prediction      

<img width="1132" alt="s3 console aws amazon com" src="https://user-images.githubusercontent.com/38410965/115980996-b65d2a80-a55e-11eb-9b2e-6881c356cf40.png">

#

> ... the various folders to drill down to ...     

### 6e     
### Postman     
click on “validation”      

<img width="1132" alt="unsmormimmas-waitpithmisssitsm somna" src="https://user-images.githubusercontent.com/38410965/115981003-c07f2900-a55e-11eb-9bd8-4678dcbae5aa.png">

#

> ... the validation data, ...     

### 6f
### Postman
click on “linear_validation.data” *

<img width="1132" alt="S3 Management Console" src="https://user-images.githubusercontent.com/38410965/115981009-cd9c1800-a55e-11eb-8bdb-44501323dd7f.png">

#

> ... and then, you know, you can open up the data, or you know look at data, or save it, and then open it up, but on a Mac you can’t do that.  So, all I did was go back to Rumi’s blog, and there was a, you can see down here at the very bottom of her page, there’s a copy of some validation data. I just copied that over. 

### 6g     
### Postman     
if you are on Windows you can download or open and look at .data files.      
On a Mac, I cannot so I go back to the demo blog      
https://aws.amazon.com/blogs/machine-learning/call-an-amazon-sagemaker-model-endpoint-using-amazon-api-gateway-and-aws-lambda/     
and copy this      

<img width="1008" alt="{data 73 49,22 3,86 91,56 7 0, 0 08752,0 07697999999999999,0 047510000000000004 0 033839999999999995,0 1809,0 0571799999999999" src="https://user-images.githubusercontent.com/38410965/115981024-e278ab80-a55e-11eb-9458-5ae4d2c4e95f.png">

from here (see it at bottom of the screenshot)     

<img width="1222" alt="Call an Amazon SageMaker model endpoint using Amazon API Gateway and AWS Lambda I AWS Machine Learning Blog" src="https://user-images.githubusercontent.com/38410965/115981028-ea385000-a55e-11eb-96c3-3b44df84d4cf.png">

#

> There it is. 

### 6h     
### Postman     
here’s the raw form which we need to alter slightly to use with Postman      

{“data”:”13.49,22.3,86.91,561.0,0.08752,0.07697999999999999,0.047510000000000004,0.033839999999999995,0.1809,0.057179999999999995,0.2338,1.3530000000000002,1.735,20.2,0.004455,0.013819999999999999,0.02095,0.01184,0.01641,0.001956,15.15,31.82,99.0,698.8,0.1162,0.1711,0.2282,0.1282,0.2871,0.06917000000000001″}

#

> So, opening up Postman.  

### 6i     
### Postman     
open Postman      

… stare at the rotating image ...     

… you are feeling sleepy ...     

(after 79 clicks … is there a choice?)     

<img width="512" alt="Pasted Graphic 125" src="https://user-images.githubusercontent.com/38410965/115981042-04722e00-a55f-11eb-834c-edb8f4a47ce4.png">

#

> ... opening ...     

### 6j     
### Postman     

… follow the spinning objects ...      
… keep looking ...     
… just relax, can you hear me?      
you are in an API and it only took you 3 clicks to get here ...     
repeat after me     
	“it only took 3 clicks in AWS to get here”      
good ...     

<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981057-16ec6780-a55f-11eb-9d2c-168437a65c0d.png">

#

> So, this is how it looks when you open it up. So, you just want to create request. 

### 6k     
### Postman     
here is how it looks when you start up      
tap on “Create a request”      

<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981068-28357400-a55f-11eb-994c-8773d1944ada.png">

#

> And you want your request page to look like this, but it doesn’t look like this when you start. 

### 6l     
### Postman     
here is how you want your Postman space to look      

<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981078-34b9cc80-a55f-11eb-9864-ea575b869627.png">

#

> So, you have to follow these steps to get it to look like that.  That means pulling down on “GET” so that becomes “POST”, and changing some other features, but you can just follow along if you like. 

### 6m     
### Postman     
to get the last slides image of Postman I start here and do these steps:      
- pull down from “GET” to “POST” (next to the “Enter request URL”)     
- “Enter request URL” … paste the URL there     
- select “Body” in the next row     
- select “raw” and “Text: in the next row     
- paste the validation data     

<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981083-41d6bb80-a55f-11eb-8982-7bb33b180e74.png">

#

> So, here’s the URL and the validation data that we’ve collected earlier. We’re going to paste those into their cells as shown here. 

### 6n     
### Postman     
here is our URL and data from earlier      

https://nqk1vty9m6.execute-api.us-east-1.amazonaws.com/test/predictbreastcancer

{“data”:”13.49,22.3,86.91,561.0,0.08752,0.07697999999999999,0.047510000000000004,0.033839999999999995,0.1809,0.057179999999999995,0.2338,1.3530000000000002,1.735,20.2,0.004455,0.013819999999999999,0.02095,0.01184,0.01641,0.001956,15.15,31.82,99.0,698.8,0.1162,0.1711,0.2282,0.1282,0.2871,0.06917000000000001″}

paste those into their spots and hit “Send”      

<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981092-51560480-a55f-11eb-94b3-b4101f0a3e13.png">

#

> And get an error.  So, you know, as usual, I have no idea why we’re getting an error, but after a couple minutes of looking at it, I just sorta figured out that the quotes, the double quotes, are different when they’re being copied over from Rumi’s blog to what this application’s used to seeing. 

### 6o     
### Postman     
Whoops!!!!      

you didn’t know to check the double quotes?      
simply retype the double quotes. they’re of a different style from the demo blog website      

<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981098-603cb700-a55f-11eb-93d8-350a4081608a.png">

#

> And there they are.  The difference between the application need, which is the “AFTER” part, and the “BEFORE” part is what Rumi has on her page.   So, the double quotes are different.  You just have to switch out four. 

### 6p     
### Postman     
here are 3 of them,      
BEFORE     

<img width="230" alt="image" src="https://user-images.githubusercontent.com/38410965/115981109-7ea2b280-a55f-11eb-9e38-b1bb5c2ba99e.png">

and AFTER     

<img width="280" alt="image" src="https://user-images.githubusercontent.com/38410965/115981119-911cec00-a55f-11eb-9ac6-a561d091c701.png">

THIS is why data scientists are paid so much.       

before      
(Don’t forget there are 4 of these sneaky double quotation marks.)     

<img width="831" alt="Headers (8)" src="https://user-images.githubusercontent.com/38410965/115981128-9ed27180-a55f-11eb-9da9-8e774c9b644d.png">

after     

<img width="832" alt="Headers (8)" src="https://user-images.githubusercontent.com/38410965/115981130-a4c85280-a55f-11eb-8f3a-3e95f6ab1d03.png">

#

> So, now you just do that, and you hit the “Send”, and you get back a “B” at the bottom, and we’re done. Thank you very much for watching.  I appreciate it.

### 6q     
### Postman     
ok now his Send      
and as the French say “et voila!”     
￼
<img width="1256" alt="Postman" src="https://user-images.githubusercontent.com/38410965/115981135-b3166e80-a55f-11eb-8142-160d312acef9.png">

The “B” at bottom of the last page stands for “Benign” and “Bye”      
Thank you for watching.     
