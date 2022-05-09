# NEAR Integration with ButterCMS

In this project you will learn how to integrate NEAR protocol with ButterCMS by creating a simple blog system in which users can access to any articles by paying the author of the article certain amount of NEAR token.  
This project is based on [ButterCMS React Starter Project](https://github.com/ButterCMS/react-starter-buttercms).


## NEAR Features

- Login
- Account Info
- Balance
- Payment transaction
- Calling contract methods

## ButterCMS Features

- Connecting to API with access token
- Fetching data from the API
- Showing blog posts

## Tech Stack

- React (create-react-app)
- Bootstrap
- React-bootstrap
- near-api-js

## Demo
![Capture5](https://user-images.githubusercontent.com/104065781/167412964-e3b108f9-5e0a-4d2b-9c66-b4a780365848.PNG)
You can test the deployed version of this project via [this link](https://buttercms-near-integration.herokuapp.com)

## Project structure

```
src\
 |--components\          # all react components
 |--assets\              # static assets (image, css)
 |--containers\          # header footer and layout
 |--templates\           # page templates for each route
 |--utils\               # other utilities
 |--App.js               # Index page
 |--index.js             # project starting point
.env                     # environment variables definition
package.json             # npm package definition
```

## Getting started

Before you start with this project you need to clone and deploy [Near Integration with ButterCMS Blog Contract](https://github.com/gurambalavadze/near-integration-blog-contract).

After you deployed the contract:

First clone the project from github

```bash
git clone https://github.com/gurambalavadze/near-integration-buttercms.git
```

In order to start the project you need ButterCMS API key. Go to [ButterCMS](https://buttercms.com) and create an account.  
Then go to [Settings](https://buttercms.com/settings), you should be able to see the Read API Token. 
![Capture2](https://user-images.githubusercontent.com/104065781/167411453-62904533-d964-4778-9023-19ddd5b48ca7.PNG)
copy that and open a terminal in the root of the porject directory and run

```bash
 echo 'REACT_APP_BUTTER_CMS_API_KEY=${your read API token}' >> .env
```

You can also do it without command line by creating .env file into the root of the project and write `REACT_APP_BUTTER_CMS_API_KEY=${your read API token}` into it.  
Don't forget to replace "your read API token" to your real token that you copied from ButterCMS admin dashboard in above commands.  
Then we need one additional environment variable which is our smart contract testnet account name. if you don't have it please first do the [Near Integration with ButterCMS Blog Contract](https://github.com/gurambalavadze/near-integration-blog-contract) project.

```bash
 echo 'REACT_APP_CONTRACT_NAME=${your smart contract account eg: mycontract.myaccount.testnet}' >> .env
```

You can also do it by opening the .env file and append the ` REACT_APP_CONTRACT_NAME=${your smart contract account}`
into new line after REACT_APP_BUTTER_CMS_API_KEY.
Now you should be able to run the project with

```bash
yarn start
or
npm start
```

and login to your wallet. Right now we don't have any article to buy so lets create some.

1.  First go to ButterCMS [blog settings](https://buttercms.com/settings/blog) and change the author first name to your testnet account name for example "myaccount.testnet" or you can create an author sub-account from your main account (read the [Near Integration with ButterCMS Blog Contract](https://github.com/gurambalavadze/near-integration-blog-contract) documentation for more info). You can also invite other authors from invite section of admin dashboard.
2.  Then go to [ButterCMS blog posts](https://buttercms.com/blog_home) and click on New Post button. You will redirected to create new post page. After filling the form click on publish button to publish it and users can see the post.
3.  Now go to the main project page (it should be hosted on http://localhost:3000) click on "view all blog post" button and you will be redirected to the blog page and in there you should be able to see your published posts.
4.  Now for testing it click on "read more" button of any of your post. You will redirect to the article page but since you haven't bought it, you can just see the summary of your article a payment button. click on Pay button and payment modal will reveal and you can set extra payment and after you click pay button you will redirect to the NEAR website to confirm the transaction. after that you will be redirected to the article page and you have access to the entire article.
5.  Now if you click on your wallet and select payments you can see the payment details.

## Structure

### utils/config.js

This is our configuration file for connecting to NEAR protocol via near-api-js library. we read the contract name from .env file. we exported environment function which returned config object for using with near-api-js based on environment that is our target. it could be whether "miannet" or "testnet" for our testing purpose we use "testnet" in this project.

### utils/near.js

In this file we create a function for connecting to NEAR with near-api-js called "initializeContract" as you can see we initialized a connection with "testnet" environment config from config.js file and we store walletConnection to window object. we also instantiate a contract object and we specify mothods we will use for this project. we use three change methods: "buyPost", "authorizeUser" and "getPayments" which is explained in the [Near Integration with ButterCMS Blog Contract](https://github.com/gurambalavadze/near-integration-blog-contract) project. we also store the contract object in window object.  
we also define functions for login and logout and see the account balance in this file.

### utils/store.js

In this file we defined functions for calling our three contract methods. For buying a post we have to specify amount of GAS because this is a payment transaction.

### utils/hooks.js

We define a hook for calling our contract mehtods called "useFetch" that is responsible for handling loading state, error and data returned from the smart contract. It will make our life easier since we no longer need to track these state in our components when we call the contract (something simillar to react-query or swr for http requests but without caching).

### index.js

We called our initializedContract function from near.js befor we render our react components so we make sure we connect to NEAR before starting our app. we also create a route for payments so we can show the list of payments to the user.
we also change some styling of this page for better user experience.

### templates/index.js

The ButterCMS starter project will create some page sections that we don't need for this project so this sections have been removed. we just want to show blog post setion.

### templates/payments.js

This is a simple react components for our payment list. we use useFetch hook to call our "getPayment" method and passing the data to PaymentList component.

### templates/article.js

This page is for showing the article content. we changed the style a little and add a pay button. so we call authorizeUser function (defined in store.js) to see if the user have access to the artilce or not and based on that we show the entire article or just a summary with pay button for read more. we also check if the user is logged in and we show login link if the user is not logged in.

### components/LoginModal.js

Simple bootstrap modal for showing users have to login to their NEAR account in order to continue. we used it on "read more" button of each post (in BlogPostList.js and BlogPostTile.js components). we used the login function defined in near.js file.

### components/PaymentModal.js

Simple bootstrap modal for payments. we define an input for extra payment. the minimum NEAR token the contract will accept is 1 NEAR. So we add extra payment plus one NEAR and passed it to our buyPost function (defined in near.js file).

### components/BlogPostsList.js

We removed the "read more" link and added a button for it so we can check whether the user is logged in befor we redirect it to the article page. we can check the login status by accessing walletConnection from window object and calling account function. it will return the account information if the user is logged in (it has account.accountId). if the user is not logged in, we show our Login modal.

### components/BlogPostTile.js

Simillar to the BlogPostList component we changed the "read more" button to check the user is logged in.

### components/wallet.js

In this components we show the user his/her NEAR account balance and a menu for logout and accessing payments page.
We used this in our Header.js component and only showing it when the user is logged in.

### containers/Header.js

We have removed the default navigation since we don't need it for this project we add login button and wallet component.
if the user is not logged in we show login button. if user is logged in we show welcome message with the user account id and wallet component. we call getBalance from near.js function and pass the balance info to the wallect compoenent.
