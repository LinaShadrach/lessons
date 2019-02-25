By now, you should have a few well-built .NET Core apps in your portfolio. In this lesson, we'll walk through how to deploy an app using Visual Studio and Azure, Microsoft's cloud platform.

## Deployment Steps

Here is a basic overview of the steps we'll take to deploy an app:

1. Ensure that the app you would like to deploy is configured correctly and is error free.

2. Create a Microsoft Account and sign up for Azure services if you have not already.

3. Create a new resource group on Azure using the Azure Portal attached to your account.

4. Using Visual Studio, push this code to the Azure app service you created using Visual Studio.

5. View your website at '[myappname].azurewebsites.net'

6. Update your app and publish the changes.


### 1. Ensure That the App is Error Free

Make sure that your app compiles and runs without throwing errors. This will make it easier to isolate what is going wrong if you have problems deploying your app.


### 2. Sign-up for Azure Services

If you do not have an account with Microsoft, [make one now](https://login.live.com/). If you do not have an account with Azure, [create a free account now](https://azure.microsoft.com/en-us/free/search/?&WT.srch=1&WT.mc_id=AID631184_SEM_oUHhx2Yc&lnkd=Google_Azure_Brand&gclid=EAIaIQobChMIxNKix7HY2gIVGMpkCh2i5w2IEAAYASAAEgJcxfD_BwE). Many of the Azure services cost money. However, a lot of services have free options for development.

### 3. Create a Resource Group

In order to create a web app service, we need to create a resource group to house our web app service. A **resource group** is a collection of related web apps, databases, and other resources.

After creating an account you should be redirectied to your personal Azure portal. You should see a dashboard. On the left side there is a list of options. Click _Resource groups_:

<img alt="Azure sidebar" src="https://www.dropbox.com/s/ffhzjtue461upnz/Screen%20Shot%202018-04-26%20at%2010.21.14%20AM.png?raw=1" width="100%">

If _Resource groups_ is not located on your sidebar, select _All services_ from the top of the side bar and click on _Resource groups_ from the list that appears.

From the _Resource groups_ page, click on _Add_ to create a new group. Give the group a logical name. Click _Create_.

### 4. Push App to Azure

Open your project in Visual Studio. After it loads and its packages are restored, right-click or CTRl-click on the project name (not the solution name) and select _Publish > Publish to Azure..._ from the dropdown menu. This will bring up a wizard that will walk you through publishing your app. If you have not signed into Azure through Visual Studio, you will be prompted to sign in now.

Once you are signed in, you should see a screen that looks like this:

![Empty App Services](https://www.dropbox.com/s/n3naeusbij08obg/EmptyAppServices.png?raw=1)

Click the _New_ button. This will open up a new dialog box. Enter in a unique name for your app next to _App Service Name:_. Notice the URL it generates below the input box. After the app is published, you and others can view it at this URL.

Next to _Subscription_, select _Free Trial_ (or whatever suscription you would like to use) from the dropdown menu.

Next to _Resource Group_, select the Resource Group you created in step 3.

For the _Service Plan_ option, unless you have already created a service plan, _Custom_ should be automatically selected. Give the plan a name that you find logical (the name of the app followed by "Plan" is a good standard), select the region where this app will be used most, and then select the pricing tier that you want to use. For practice deploying, _F1 - Free_ is a good option. If you are publishing your app for production, you'll want to explore the other pricing tiers. 

After you click _Create_, this dialog box should appear:

![App Service Dialog Box](https://www.dropbox.com/s/uq1338rfyvv452v/Screen%20Shot%202018-04-26%20at%207.48.49%20AM.png?raw=1)

Click _Ok_ and wait for the application to finish publishing.

### 5. View Application

Navigate to [yourwebappname].azurewebsites.net to view your app!

### 6. Publish Changes

To publish changes to your app, you simply have to use the same publishing editors in Visual Studio. Right click or CTRL click on the project and hover over the _Publish_ option. You should see a second publishing option under _Publish to Azure_. It should say _Publish to [YourAppService]_. Select this and your app will automatically try to publish.
