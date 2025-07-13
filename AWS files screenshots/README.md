# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) Deploy React App with Full CI/CD Pipeline on AWS | GitHub + CodePipeline + S3 🚀

<div align="center">

  <br />
      <img src="https://github.com/user-attachments/assets/9f9b55f3-c9c2-4b87-97c7-030be3464a83" alt="Project Banner">
  <br />

<h3 align="center">Deploying a React app using EC2 and Terraform</h3>

</div>

## 🚨 Tutorial

This repository contains the steps 

## <a name="introduction">🤖 Introduction</a>

In this tutorial, you'll learn how to build a fully automated CI/CD pipeline using AWS CodePipeline, CodeBuild, and Amazon S3 to deploy a React.js application hosted on GitHub. Say goodbye to manual deployments, every time you push to your repo, your app will automatically build and deploy to a static website on S3!


## <a name="steps">🔧 How it Works:</a>
 
1. Push code to the `main` branch on GitHub.
2. AWS CodePipeline detects the change.
3. AWS CodeBuild installs dependencies and builds the React app.
4. The build output is deployed to the S3 bucket.
5. S3 serves the app as a public static website.

## ➡️ Step 1 - Setup your React.js App on GitHub

First, we’ll set up a React app by cloning the React app from my GitHub repository. You can use your own or follow along with mine. Make sure the app is committed to GitHub.


```bash
git clone https://github.com/Ajaytipte/aws-codepipeline-react-s3.git
```

## ➡️ Step 2 - Create S3 Bucket for Hosting

For the deploy provider we are going to use Amazon S3, we will create an S3 bucket.
1. Head over to the S3 service.
2. Click Create bucket.
3. Name it something unique like `my-react-cicd-demo`

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20211712.png?raw=true)

Once the s3 bucket is created, leave it for now, as we will come for it to finish the setup later.


## ➡️ Step 3 - Create CodePipeline

Now the fun part—building the pipeline.

1. Go to AWS CodePipeline, click Create pipeline.
2. Name your pipeline: `reactapp-cicd-demo`
3. Choose a new service role or an existing one.

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20212118.png?raw=true)

4. Add source stage:
<br>- Source provider: GitHub (connect your GitHub account).
<br>- Select your repository and branch.

⚠️Note: Make sure you select the repository that we cloned in Step 1

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20212227.png?raw=true)

<br>- Once you are connected to your Github and select your repository, then choose "Next"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20212740.png?raw=true)

5. Add build stage:
<br>- Provider: AWS CodeBuild.
<br>- Choose "Create project"

Let's proceed to next step and create the CodeBuild Project.

## ➡️ Step 4 - Create CodeBuild Project

Now let’s set up CodeBuild, which will handle building the React app.

1. Go to CodeBuild, click Create Build Project.
2. Name it something like `react-cicd-pipeline-demo`

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20213346.png?raw=true)

3. Choose a managed image: aws/codebuild/standard:6.0 (or latest).
4. Under build specifications, choose "Use a buildspec file" 

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20213524.png?raw=true)

5. Inside your GitHub repo, create a file named `buildspec.yml` in the root:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 18
    commands:
      - echo Installing dependencies...
      - npm ci --legacy-peer-deps

  build:
    commands:
      - echo Building the React app...
      - npm run build

artifacts:
  files:
    - '**/*'
  base-directory: dist
  discard-paths: no

```

⚠️Note: This file tells CodeBuild to install dependencies, build the app, and copy the contents of the build/ folder as artifacts.

6. Back to the CodeBuild Project, keep the rest as default and choose "Continue to CodePipeline"
7. Then the CodeBuild project will be create and added to the build stage as shown below, then choose "Next"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20213225.png?raw=true)

8. Add deploy stage:
<br>- Provider: Amazon S3.
<br>- Bucket: Select the one you created earlier `my-react-cicd-demo`
<br>- Extract file option: YES, choose "Next"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20214321.png?raw=true)

<br>- Lastly, review all the configuration and click "Create pipeline"

Once the pipeline is successfully created, you’ll see it run through the `source` `build` and `deploy` stages.

## Let's finish our S3 Buckect configuration

1. Go to Amazon S3 console
2. Select our existing S3 bucket `my-react-cicd-demo`
3. You should see the S3 bucket with objects inside, extracted from our CodePipeline.
4. Now let's make this S3 Bucket public:
<br>- On the top bar, choose "Properties"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20214936.png?raw=true)

<br>- Scroll down to "Static Website Hosting" and click "Edit"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20215148.png?raw=true)

<br>- Under "Static Website Hosting", choose "Enable"
<br>- And specify `index.html` as the index document, then click "Save"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20220906.png?raw=true)

<br>- Next, edit some permissions, still on the tob bar choose "Permissions"
<br>- Uncheck "Block all public access" to allow public access, then click "Save changes"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20220906.png?raw=true)

<br>- Next, we will add a bucket policy to allow public read access inside our s3 bucket. Here's the sample policy you can use:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```
⚠️ Replace: `your-bucket-name` with your actual bucket name, then click "Save"

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20220947.png?raw=true)

<br>- Go back to the S3 Bucket console, on the top bar, choose Objects, then click on `index.html`
<br>- To visit your React.js App, click on the Object URL.

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20221003.png?raw=true)

<br>- You should see your React.js App running on Amazon S3

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20221042.png?raw=true)


## ➡️ Step 5 - Test the Pipeline

Let’s test the whole pipeline. I’ll make a small change to the homepage text and push it to GitHub.

As soon as the code is pushed, CodePipeline is triggered. You’ll see it run through the source, build, and deploy stages.

![Image](https://github.com/Ajaytipte/aws-codepipeline-react-s3/blob/main/AWS%20files%20screenshots/assets/Screenshot%202025-07-12%20214619.png?raw=true)


## 🗑️ Clean Up Resources

When you’re done, clean up your AWS resources to avoid charges.
