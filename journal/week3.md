# Week 3 — Decentralized Authentication

## Objective
In this week we are going to implement decentralized authentication, this mechanism to let you be able to login, logout, and reset the password, also we will see how to integrate it to the backend application with custom login page to be used in our web application.

### Provision Cognito User Group

Go to your AWS Console Page:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224528931-d00230e9-d169-4e44-b3ca-d039809f38e8.png" alt="Sublime's custom image"/>
</p>

Step 1- create a new user pool:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224528937-4ac16fc6-10c4-4559-9335-9cbb9259ca49.png" alt="Sublime's custom image"/>
</p>

Select the provider type:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224528961-4b87a142-298d-4803-a066-35b6100fc2c7.png" alt="Sublime's custom image"/>
</p>

User pools is when you are making a web application and you want to add login and sign up and you want to have that managed externally.

Federated identity provider: used when you want to be able to log in using (social identity from another provider).


Select sign in options:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224528979-1d7403ff-1bbc-415f-aa9a-9b2c6672d986.png" alt="Sublime's custom image"/>
</p>
Select the needed options for your sign in (we need username and email).

Step 2 - Now select the password policy 
We will make it default…

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224528996-2efa1846-18d7-4d0d-a7f8-c5bdad581bc0.png" alt="Sublime's custom image"/>
</p>

Select the MFA option:
(Select No MFA) – Just for saving cost-- 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529014-14d0f92c-1c25-4c2c-b5ad-d2152562ba0e.png" alt="Sublime's custom image"/>
</p>

Select user account recovery:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529042-bd6ee4ff-1e90-4982-ba81-cec4fba7db52.png" alt="Sublime's custom image"/>
</p>
To configure how the users will recover their accounts when they forget their passwords. Here we can activate the self-recovery service, and also select the delivery method (will select Email only -saving cost-).	

Step 3 – Configure sign-up configuration.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529057-8dfd89d0-14d8-41c7-b25d-6933afdb7d49.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529060-9985c208-a146-4580-8522-638f1fe0271a.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529062-fa89d45a-63d0-41c3-b4e4-b104cd24ba9e.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529081-d1c1078a-2597-4786-a587-c2f703dbfd3c.png" alt="Sublime's custom image"/>
</p>
We will ask only for the name and preffered name (username).


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529127-0c5450aa-4f26-439a-bb40-38c195f19e78.png" alt="Sublime's custom image"/>
</p>
It is a way to store data alongside with your user identity.

Step 4 - Configure message delivery.
Select the email provider:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529164-9a52ff6d-f54a-4205-a204-e5fbc7cf0a05.png" alt="Sublime's custom image"/>
</p>
SES used for production with a higher number of emails, it needs a pre configuration before… also it needs a custom domain.
We will select send email with Cognito (for development) 


Add the From email:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529177-dea90dc0-0b6d-48ae-9301-125aae0837d4.png" alt="Sublime's custom image"/>
</p>

Step 5 - Integrate your app.
Select a name for the user pool.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529195-53856491-bab1-423b-a58f-87be1ccf5a56.png" alt="Sublime's custom image"/>
</p>

Never use the Hosted UI, 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529202-32b9af07-825a-4587-a5b1-43d94950704d.png" alt="Sublime's custom image"/>
</p>

Select the app type:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529208-134ba224-e3b3-439a-b4d8-02de183a81f0.png" alt="Sublime's custom image"/>
</p>
As shown, we are going to select the Public client, because we are developing modern application. Usually when we are creating monolithic application every things is server-side.

Add the app name:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529219-ad48b0ab-c1ef-4444-9b1a-dff4e8334c47.png" alt="Sublime's custom image"/>
</p>

Create user pool:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529223-882d5bb8-7c16-4c92-bd14-dc8cb73b62ef.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529226-52a8bfac-d9b1-4b03-9823-c2fbc88e12ef.png" alt="Sublime's custom image"/>
</p>

### Install and Configure Amplify

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>






<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>




<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>






<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>
