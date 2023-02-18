# Week 0 — Billing and Architecture

## C4 Architecture Models
C4 models (also known as Context, Containers, Components, and Code) is a software architecture model that provides a way to visualize and organize the structure of a software system. It is based on the idea of breaking down a system into four distinct aspects: context, containers, components, and code. The C4 model provides a way to communicate software architecture by providing a common language for describing the structural elements of a system and their relationships.


## Conceptual Design using Lucid Chart
### Modification for Lucid
* First of all to add AWS Icons go to &rarr; Import Data &rarr; Shapes &rarr; in the left hand side choose “AWS Architecture 2021”.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865559-ebabd715-5083-4aad-9bcc-8c26f7d0a89e.png" alt="Sublime's custom image"/>
</p>


* If you are messy man and you want to get rid of diagram boundaries got to &rarr; File &rarr; Page Setting &rarr; select “infinite canvas mode”.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865722-2712d9f5-6325-43c4-9f27-54d6badd39f5.png" alt="Sublime's custom image"/>
</p>


* To remove the Grid &rarr; go to upper bar &rarr; select View &rarr; select Grid &rarr; turn off show grid.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865846-f151628e-23b4-46c8-ba30-2e098640bd1c.png" alt="Sublime's custom image"/>
</p>

<br/>
<br/>

### How to make a Napkin design? And what should we put in it?
1- First we start with the user we want to know what the user able to do in our project, add it to your diagram and change the name:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865970-683ce90a-12a1-40dc-bb0e-67bbf10da811.png" alt="Sublime's custom image"/>
</p>

2- We want to create a Twitter killer, from conceptual perspective we want to know what is the things that we want this user to be able to do. (it is very high level we just put a block of processes).
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866133-04a60415-6f76-40b3-be19-f3f5d480f8c9.png" alt="Sublime's custom image"/>
</p>

3- Here we will asks our selves one dump question (do we want to have the users be unique? Is every user will have their own identity?), the answer yes. So we need to have a place that stores stateful data (for 2 things, 1 for the tweets that will be deleted, 2nd for the actual identity of the person), in our Napkin we will assume that both databases are in one big blob:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866160-0952bdc0-9cff-4b0f-92c4-69b125751362.png" alt="Sublime's custom image"/>
</p>

4- Also, we need a massaging system:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866205-6bc7ef2e-4fe7-47a2-85ea-7ebb37ff4001.png" alt="Sublime's custom image"/>
</p>

5- We want the app to be highly available so we are going to need a load balancer in the frontend.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866251-97e79fc9-fc6c-4143-a5da-81b49dad41bd.png" alt="Sublime's custom image"/>
</p>

6- Now to complete the connection path, there is a dump question, will load balancer talk directly to the backend? (Answer: because like there is API like people could hit the API) (yes for backend there is a load balancer because if the API is going to be exposed -and that how frontend talking to the backend API-) (to save the cost we will use single load balancer for frontend and backend)
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866288-b9ab3e33-5513-48a8-a116-853a65d7d9ed.png" alt="Sublime's custom image"/>
</p>


7- Another question, is there a real time component? Is the messaging system real time? (Ans: Yes we will use the Dynamodb as a massages system with appsync as push real time update).
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866311-eeb4f9dd-c112-4b95-948d-350188f68a65.png" alt="Sublime's custom image"/>
</p>


8- If we looking to the twitter Napkin design we will see a Search service. (it will interact with backend because if we did every thing to the backend then if you had an API that you wanted to search youd have to be here)
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866334-00aaefef-4e17-4c76-8228-6edbb0867122.png" alt="Sublime's custom image"/>
</p>


9- Remember to only add in Napkin diagram the main focused business things, so if for example ddos is important add it, if any security service important add it, and remember this is not the final arch it is just a very high level, for our management the timeline is an important and distinguisher we should add it.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866364-77923738-5d47-4cdb-8e88-22412d2a7a0b.png" alt="Sublime's custom image"/>
</p>

<br/>
<br/>

## Creating AWS Account
Already DONE "Easy"

<br/>
<br/>

## Creating Budget for AWS
Go to your Root Account &rarr; Search for AWS Budget Service &rarr; Click “create budget” &rarr; we can select between template, or customize (we will select zero spend budget) &rarr; select the template (zero spend) &rarr; Select the name of your budget (we select My Zero-Spend Budget) &rarr; Select the email we will send the alert to &rarr; finally click “create budget”
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866530-7b4370b9-a4b1-4bb9-b187-266efcd08ee4.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866598-500df430-5d57-4b79-8a1d-e46a81a1813c.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866604-0d62d451-8a99-4bb7-8795-b845280b4dfc.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866630-ccd49bf5-8f37-438e-977c-2159e7f83189.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866648-3599fd6d-99ad-4616-8780-578c488e3125.png" alt="Sublime's custom image"/>
</p>





