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

Now let’s back to GitPod :D
We will configure AWS Amplify, which is an SDK for a bunch of common serverless libraries, it is a hosting platform, it is a way of provisioning service applications, it is low code solution. And the only way that we can use Cognito Client Side is by using the amplify JavaScript library, (what is I want just to use Cognito No WAY!!).


Step 1 – Install Amplify
Make sure that you are in the front-end directory. 
```
npm i aws-amplify --save
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529324-cd637e73-d62b-4bd6-92b2-4cf42157dcb7.png" alt="Sublime's custom image"/>
</p>

We used - - save in amplify installation because we need to have it to login it should be with app Dependences not Dev Dependencies.
What is the difference between both?


Dependences are libraries that are required to make the application work, and you always want them installed.
Dev Dependences are libraries that you only want to be installed when you are using them for development, but when application shipped to production they are not shipped.


All dependencies are available in ‘package.json’ file in my repository, because we use the ‘--save'.


Notice the aws-amplify is available after done with installation…


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529347-48a6c553-e729-43e1-9c60-08b5948c9c57.png" alt="Sublime's custom image"/>
</p>

Go to frontend-react.js/src/App.js add below import code:
```
import { Amplify } from 'aws-amplify';
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529357-998b63f9-be53-4c95-8ff7-de84d8543dd2.png" alt="Sublime's custom image"/>
</p>

Also add the Amplify configuration commands:
```
Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_AWS_PROJECT_REGION,
  "aws_cognito_identity_pool_id": process.env.REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529376-047e0b10-fec7-4fb1-8d7b-9fdeb2ff382d.png" alt="Sublime's custom image"/>
</p>

Let’s explain the configuration code:

Process.env: env for environment variables, process is what is the current processes running (processes is when you run an app it runs a version, so this running thing is the process)


So, we need to set these environments variables.


Open your docker compose file and add them in environment section:
```
REACT_AWS_PROJECT_REGION:
REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID:
REACT_APP_AWS_COGNITO_REGION:
REACT_APP_AWS_USER_POOLS_ID:
REACT_APP_CLIENT_ID:

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529409-f429f413-7abc-4697-a772-9cd8058883ea.png" alt="Sublime's custom image"/>
</p>

Now we need to fill them:
But notice there are a trap, we don’t have identity pool so we should remove it.
Now go to console and find your user pool id.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529419-3f0a59fc-cd5d-4818-a932-8c5befa2abd9.png" alt="Sublime's custom image"/>
</p>

Go to find the app client id:

Under App integration tap:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529425-54e1085f-af1b-43f0-8828-353077fd8f59.png" alt="Sublime's custom image"/>
</p>

We found another trap which is the environment variable name:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529434-059c8f1b-cc14-46d0-84e0-e2283115b023.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529437-039eebdd-99f0-4b9d-b62b-a4eccb9bead8.png" alt="Sublime's custom image"/>
</p>

Step 2 – Show components based on logged in or logged out.



We will make some changes in our code conditional, what we mean that because sometimes if we authenticate it will show things as we are logged in if we were not logged in it will not show certain things.



So we will start from HomeFeedPage.js:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529462-07335874-b647-484f-a514-0d2c4a8f0b9d.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529468-14574d1d-cbc7-49d4-bf0c-fd5717250882.png" alt="Sublime's custom image"/>
</p>

Add:
```
import { Auth } from 'aws-amplify';
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529475-91f0a0a0-1456-4a68-a9ad-67f7ad2a912a.png" alt="Sublime's custom image"/>
</p>

Also add:
```
const [user, setUser] = React.useState(null);
```
but it is already available. !!

Now we will replace the mocked user with authenticate.
```
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529501-b72379ed-facc-4ec0-9849-a4882ab120e8.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529506-14ddd5f1-4d7d-4603-a661-a6973b119d0a.png" alt="Sublime's custom image"/>
</p>

Now go to DesktopNavigation.js:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529520-2fb2d9e7-9873-4590-8a84-bd9c3544b7ad.png" alt="Sublime's custom image"/>
</p>

Step 3 – Go to profileinfo file and made the changes:

Replace

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529528-141db832-3e5f-4068-bd32-cb7a256fd223.png" alt="Sublime's custom image"/>
</p>

With:
```
import { Auth } from 'aws-amplify';
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529554-f5e1e7b4-dba6-4cb8-8822-ed8f39ab18e8.png" alt="Sublime's custom image"/>
</p>

Also down below we was using cookie, remove it:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529562-e4ccedc7-fe16-488e-99c2-6911b4adeac2.png" alt="Sublime's custom image"/>
</p>

And add the below: 
```
const signOut = async () => {
  try {
      await Auth.signOut({ global: true });
      window.location.href = "/"
  } catch (error) {
      console.log('error signing out: ', error);
  }
}

```

Lets try our App if it running or not?

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529580-76a07643-7b79-4a90-b3b5-cfb24c5df411.png" alt="Sublime's custom image"/>
</p>

Notice that last environment variable are different what in the above, so make it similar as below:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529593-dc87af3e-8029-4690-8562-8738d0297023.png" alt="Sublime's custom image"/>
</p>

Also this one is wrong:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529624-312695b7-4d05-47e1-9a3d-6b0d51d42574.png" alt="Sublime's custom image"/>
</p>

Make it:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529631-cfbcd927-9934-492e-bd7a-20d59826bfd6.png" alt="Sublime's custom image"/>
</p>

And it is working:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529649-ae5ff1d5-1f67-4742-a251-0816ccaeb654.png" alt="Sublime's custom image"/>
</p>

Step 4 – Sign in Page
Go to frontend/src/pages/SigninPage.js and add below code:

Replace cookies:
```
import { Auth } from 'aws-amplify';
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529663-b6977274-4cf2-4e75-8d3b-e29b24c1a9b9.png" alt="Sublime's custom image"/>
</p>

Add also:
```
onst onsubmit = async (event) => {
    setErrors('')
    event.preventDefault();
    try {
      Auth.signIn(username, password)
        .then(user => {
          localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
          window.location.href = "/"
        })
        .catch(err => { console.log('Error!', err) });
    } catch (error) {
      if (error.code == 'UserNotConfirmedException') {
        window.location.href = "/confirm"
      }
      setErrors(error.message)
    }
    return false
  }

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529680-0fc25620-6d76-47e4-a1a0-5b52b7bf827e.png" alt="Sublime's custom image"/>
</p>

Let’s check this:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529690-5ba01e9f-ca0f-4732-b422-a0853f2f3745.png" alt="Sublime's custom image"/>
</p>

Now back to AWS Cognito and add a user manually:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529700-b984ff04-0f33-4fa4-9a94-9298a588cbaf.png" alt="Sublime's custom image"/>
</p>

After trying:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529704-1b537956-b604-4fb1-8427-f3b5795243c5.png" alt="Sublime's custom image"/>
</p>

Actually, the problem is from the password force change:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529714-68ec245c-066e-4bab-825e-f39f81a45a47.png" alt="Sublime's custom image"/>
</p>

And from console we are not able to confirm the user:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529722-91ff9b20-6a48-4396-b967-75e09958850f.png" alt="Sublime's custom image"/>
</p>

So, use the below aws cli command to force confirmation:
```
aws cognito-idp admin-set-user-password --username lahham392 --password Test392! --user-pool-id us-east-1_ENZaTSpo1 --permanent 
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529735-07fe1f0b-8bcf-4343-9682-4fd7f0ff653b.png" alt="Sublime's custom image"/>
</p>

Now it should work, go to the sign in page and try:

Still I’m getting this error:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529747-6908afd4-b199-4008-ae71-bd9c6d64db59.png" alt="Sublime's custom image"/>
</p>

When I checked the environment variables of my frontend container I didn’t find any of them!!!!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529757-b9f501f2-cf1b-4603-b800-84e3101d8fed.png" alt="Sublime's custom image"/>
</p>

And yes, it is not set correctly it should be under frontend environment not backend ☹

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529767-5984bb6e-265b-4bf1-90ce-85a1e4ae04ae.png" alt="Sublime's custom image"/>
</p>

Like this:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529780-33ddaf6b-89fa-4a9b-acf4-9252b62fd5a9.png" alt="Sublime's custom image"/>
</p>

Now let’s start docker compose up again and check the environment variable in front end:


Yes, it is saved…

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529792-d6edfb24-8a85-4480-b1ba-a2d5bb9bc13e.png" alt="Sublime's custom image"/>
</p>


Check the app:
It Woooooorkssss!


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529801-f9b11027-ee92-483f-921e-4a3abecbaf73.png" alt="Sublime's custom image"/>
</p>


Now let’s add new user:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529810-5ebfab83-043c-4e97-ad84-99d49c3046a3.png" alt="Sublime's custom image"/>
</p>

AWS CLI and force activate it:
```
aws cognito-idp admin-set-user-password --username lahham392 --password Test123! --user-pool-id us-east-1_e1omBwpIK --permanent 
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529819-0ef2100b-603c-4d84-bb15-e5c786b15ec2.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529823-3b4dfa97-6c0f-4d2d-978f-edbe1e1b3c6e.png" alt="Sublime's custom image"/>
</p>

Test our page:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529836-24b3e804-2858-476b-a975-50946088b335.png" alt="Sublime's custom image"/>
</p>

It’s logged in!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529845-3cd8e2ab-bd3c-4ea7-b352-c4106c1b0cc4.png" alt="Sublime's custom image"/>
</p>


Sign-out

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529855-c207c7d3-95e5-4fb3-93fc-dcbf7ec6141e.png" alt="Sublime's custom image"/>
</p>

We can add the user name and preferred name of Cruddur user:
Go to User &rarr; User attributes &rarr; Preferred Name


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529874-6bc06021-22c6-4c5b-ab49-85bc1264bf37.png" alt="Sublime's custom image"/>
</p>

Before:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529881-a348cc75-f6cc-4a4d-af69-367b037772f4.png" alt="Sublime's custom image"/>
</p>

After:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529888-cb0c0796-6a50-4682-8917-9d5a5264b272.png" alt="Sublime's custom image"/>
</p>


### Now we are going to make Signup page:

1-	Delete the current user from AWS Cognito.

2-	Move to SignupPage.js

3-	Replace the cookies with “import { Auth } from 'aws-amplify';”


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529927-1df83859-62dd-4342-89e6-6d3e48befe4d.png" alt="Sublime's custom image"/>
</p>


4-	Replace onsubmit code with:
```
const onsubmit = async (event) => {
  event.preventDefault();
  setErrors('')
  try {
      const { user } = await Auth.signUp({
        username: email,
        password: password,
        attributes: {
            name: name,
            email: email,
            preferred_username: username,
        },
        autoSignIn: { // optional - enables auto sign in after user is confirmed
            enabled: true,
        }
      });
      console.log(user);
      window.location.href = `/confirm?email=${email}`
  } catch (error) {
      console.log(error);
      setErrors(error.message)
  }
  return false
}

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529954-04a4d4f7-a86e-4c26-aa85-29f2ea08b4e8.png" alt="Sublime's custom image"/>
</p>


### Make the confirmation page:
1-	Move to ConfirmationPage.js

2-	Replace the cookies with “import { Auth } from 'aws-amplify';”

3-	Replace onsubmit code with: (resend code):
```
const resend_code = async (event) => {
  setErrors('')
  try {
    await Auth.resendSignUp(email);
    console.log('code resent successfully');
    setCodeSent(true)
  } catch (err) {
    // does not return a code
    // does cognito always return english
    // for this to be an okay match?
    console.log(err)
    if (err.message == 'Username cannot be empty'){
      setErrors("You need to provide an email in order to send Resend Activiation Code")   
    } else if (err.message == "Username/client id combination not found."){
      setErrors("Email is invalid or cannot be found.")   
    }
  }
}

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224529997-434cb232-991d-4432-be9f-db2eb97ba489.png" alt="Sublime's custom image"/>
</p>

4-	Replace onsubmit with:
```
const onsubmit = async (event) => {
  event.preventDefault();
  setErrors('')
  try {
    await Auth.confirmSignUp(email, code);
    window.location.href = "/"
  } catch (error) {
    setErrors(error.message)
  }
  return false
}

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530009-b657743a-5eb7-4764-b0c6-663a29248d94.png" alt="Sublime's custom image"/>
</p>

Test your changes:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530015-035e7304-558e-4d3f-8e21-1d2ab14fbc42.png" alt="Sublime's custom image"/>
</p>

We got the following error:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530027-1105071a-390f-4400-bb55-73c264df66ef.png" alt="Sublime's custom image"/>
</p>

We suppose to use only Email!!!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530045-1aba172e-c745-4638-97e1-132ea5bf93e8.png" alt="Sublime's custom image"/>
</p>
So now we should recreate a new User Pool, and update the environment variables:



Now lets test the app:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530056-0becb66b-1db2-4161-bc21-aec7202c3fb1.png" alt="Sublime's custom image"/>
</p>

Restart your Docker compose and try again:

Worked!!


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530078-a497e744-a0a9-4028-a73c-52bfd020a2c7.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530084-5d3f1a39-1bf7-4a29-81aa-1b0105ca2353.png" alt="Sublime's custom image"/>
</p>

But it back to the main page without signin:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530098-62fe8f25-ef40-42e3-a484-08e3dabe8f80.png" alt="Sublime's custom image"/>
</p>

If we checked AWS Cognito, we could see the user is added:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530107-516f7e9a-adbf-424e-9be2-d2bba729d4f0.png" alt="Sublime's custom image"/>
</p>

Try to sign in:


And Yes it logged in!


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530130-86e3baac-0da8-4e01-93a3-5dfb50840449.png" alt="Sublime's custom image"/>
</p>

### Set Recovery Page:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530147-395e610c-cb57-4590-b7da-4c8c8da7f31d.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530151-67274a02-22b6-4b63-85a7-402b2d10c6a4.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530155-9aecc0a2-a728-441c-b3e6-aea20f375c7a.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530161-71df88dd-f629-4522-97c5-c79147868902.png" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530165-cff2e2f6-ddff-4fdd-a5de-dabe7873e5c8.png" alt="Sublime's custom image"/>
</p>

## Cognito Configuration for Backend

In the above part of project, we let Cognito be authenticated and a lot of things in front-end but we do have to make sure that we protect our API endpoints, the idea is we want to pass along our access token ‘which we store it in our local host’ so we need to pass along with our API call.

1-	Add in the `HomeFeedPage.js` a header to pass along the access token:
```
  headers: {
    Authorization: `Bearer ${localStorage.getItem("access_token")}`
  }

```

So, this will pass along with that header for the authorization token.

Now how do we read that in our back end?


2-	Update CORS, move to app.py and add below code.
```
cors = CORS(
  app, 
  resources={r"/api/*": {"origins": origins}},
  headers=['Content-Type', 'Authorization'], 
  expose_headers='Authorization',
  methods="OPTIONS,GET,HEAD,POST"
)

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530269-80df1190-3f3b-422e-b270-1ed5c99eb153.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530271-fcb4b7b8-b769-4625-b691-f1b7a0c4a4cd.png" alt="Sublime's custom image"/>
</p>

3-	Now after editing the front end and putting the authentication token in the header to pass it to the backend, we are now going to modify the backend and check if the token passed or not:


In App.py add the following code:
```
  app.Logger.debug("AUTH HEADER")
  app.logger.debug(
    request.headers.get('Authorization')
  )

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530303-114f19e5-65fc-4e09-99d9-d341cb75fec9.png" alt="Sublime's custom image"/>
</p>

Now, go to logger of Backend and check if the token shown there or not?

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530316-f94a4030-c31f-4358-b3fe-2fdf54b4db3a.png" alt="Sublime's custom image"/>
</p>
There is no token passed.

When we checked the DevTool for session, I found that the problem from CORS, so let’s check CORS why not updated!
We can see logger written as Logger, so lets fix it and try again



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530343-b83bebab-fe2f-407a-a3d2-9c6a6092c39d.png" alt="Sublime's custom image"/>
</p>

Its shows Bearer null, I think we should login to see the token:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530369-82839e39-5871-4b52-a30a-a3515a8c5561.png" alt="Sublime's custom image"/>
</p>

Now we can see the tokens :)

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/224530393-af2a8408-1fae-4267-82a7-89747094d068.png" alt="Sublime's custom image"/>
</p>

Now what do we do with this access token? That access token “JWT” and the idea with that we need to decode it in order to extract information and verify that its correct, the idea is that we don’t have to go out to a server to grabbing a key to match it again with Cognito, we don’t have to do anything with server side “all handled by Cognito.”  

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
