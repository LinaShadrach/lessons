You've been hard at work creating Angular applications for nearly two weeks! You're probably ready to begin sharing them with the world. In this lesson we'll discuss how to deploy and host Angular projects. This will make it easier for potential internship companies and employers to preview your project, and allow friends and family that may not be GitHub and command line-savvy to sample the awesome work you've already completed!

There are many different ways to deploy an Angular application. We'll use Firebase's hosting service in this lesson; both because it's free of cost, and because our applications are likely already set up with Firebase projects to manage databases.

You are required to deploy your project for this week's code review. We recommend and provide information here about deploying via Firebase (because it's easy to set up), but you are also welcome to experiment with Heroku or another hosting service for your code review. Regardless, it's important to learn to deploy and maintain deployed applications so they can become part of your portfolio.

## Configuring for Deployment

First we need to prepare our project for deployment. This includes creating a production build, installing Firebase's command line tools, and creating several configuration files Firebase requires to host applications.

### Production Build

We'll begin by building our project in its production environment. A production environment has minified code for improved browser performance, and doesn't include development-only dependencies.

First, we'll want to make sure we have all our npm dependencies installed and ready to go (especially if we're working on a new machine). Re-install all dependencies now, just to be sure:

```
$ npm install
```

Next, we can build a production version of our project with Angular CLI by navigating to the root level of its directory and running with the following command:

```
$ ng build --env=prod
```

It may take a few moments for Angular CLI to build the production version of the project. When it's complete you'll be met something like this:

<pre>
Hash: 99157493c536cdbb8906                                                               
Time: 34054ms
chunk    {0} main.670633420d0a1c1a034b.bundle.js (main) 22.7 kB {2} [initial] [rendered]
chunk    {1} styles.d55fd6ba605ef80a93a6.bundle.css (styles) 120 kB {3} [initial] [rendered]
chunk    {2} vendor.70680c9e468bcfc68342.bundle.js (vendor) 3.28 MB [initial] [rendered]
chunk    {3} inline.2099b135155560f81ea1.bundle.js (inline) 0 bytes [entry] [rendered]
</pre>

There should now be a _dist_ folder in your project directory. This contains your production build. You can open it and check out your new, minified code if you'd like!

### Create Firebase Project

Next we need to prepare Firebase for the deployment of a new project.

If the project you're deploying does **not** currently have a corresponding Firebase project/database on the Firebase website, visit your account online and create a new project. _Even if the project doesn't require a database_ it will still require a project in your Firebase account in order for Firebase to host it.

If the project you're deploying **already uses** Firebase for its database, you may simply use the _same_ Firebase project containing your database to host the application.

### Firebase-Tools

As soon as the project has a dedicated _Firebase_ project to host it, we'll create several configuration files in our project directory that Firebase requires to host a project.

To do this, we'll use the `firebase-tools` npm package. This package provides additional Firebase capabilities directly from the command line.

We can install it using npm:

```shell
$ npm install -g firebase-tools
```

Once installed, run the login command:

```shell
$ firebase login
```

This will open a browser window prompting you to log into your Google/Firebase account.

Next, initialize Firebase in the root directory of the Angular project you'd like to deploy:

```shell
$ firebase init
```

You'll be prompted with the following question:

<pre>
You're about to initialize a Firebase project in this directory:

  /Users/student/Desktop/your-project-directory-name

? What Firebase CLI features do you want to setup for this folder?
❯◉ Database: Deploy Firebase Realtime Database Rules
 ◉ Hosting: Configure and deploy Firebase Hosting sites
</pre>

Note that both of the options above should be selected (that's why the circle is filled in).

Use your keyboard's arrow keys to select _Hosting_.

Firebase will then ask you to select which of your existing Firebase projects should host this application:

<pre>
=== Project Setup

First, let's associate this project directory with a Firebase project.
You can create multiple project aliases by running firebase use --add,
but for now we'll just set up a default project.

? What Firebase project do you want to associate as default? (Use arrow keys)
your-firebase-project-names-here
</pre>

Your current Firebase projects will be listed. Using the arrow keys to navigate, select the one that corresponds to the project you're deploying. If this project doesn't have a Firebase a database, select the project you created a moment ago. If it does, select the project containing its database.

Next, create a file in the top-level of your project directory named _database.rules.json_. Insert the following into it:

<div class="filename">database.rules.json</div>
```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}

```

This informs Firebase that your application should have permission to both read and write to your database.

Next, you may have noticed the Firebase CLI created a _firebase.json_ file in the top-level of your project, too. We'll insert the following code in this file:

<div class="filename">firebase.json</div>
```json
{
  "database": {
    "rules": "database.rules.json"
  },
  "hosting": {
    "public": "dist"
  }
}

```

Here, we're telling Firebase where to find the database rules we've just created, and letting it know that the code for our live-hosted site can be found in the _dist_ directory, where Angular CLI created our minified production build.

Finally, the Firebase CLI should also have created a _.firebaserc_ file in the top-level of your directory too. Open this, and simply confirm it contains the name of the corresponding project on Firebase you'd like to deploy to:

<div class="filename">.firebaserc</div>
```
{
  "projects": {
    "default": "your-project-name"
  }
}

```

## Deploying to Firebase

Your application should now be correctly configured for deployment. You can now deploy with the `deploy` command:

```shell
$ firebase deploy
```

You should be met with the following. It may take several minutes for  Firebase-Tools to complete the deployment process. This is normal.

<pre>
=== Deploying to 'your-project-name'...

i  deploying database, hosting
✔  database: rules ready to deploy.
i  hosting: preparing dist directory for upload...
✔  hosting: dist folder uploaded successfully
✔  hosting: 2 files uploaded successfully
i  starting release process (may take several minutes)...

✔  Deploy complete!

Project Console: https://console.firebase.google.com/project/your-project-name/overview
Hosting URL: https://your-project-name.firebaseapp.com
</pre>

As soon as the command line reads _Deploy complete!_ you can use the following command to navigate your new, hosted application:

```
$ firebase open
```

This will provide a menu of options to scroll through:

<pre>
? What link would you like to open? (Use arrow keys)
❯ Project Dashboard
  Analytics
  Database: Data
  Database: Rules
  Authentication: Providers
  Authentication: Users
  Storage: Files
  Storage: Rules
  Hosting
  Hosting: Deployed Site
  Remote Config
  Remote Config: Conditions
  Test Lab
  Crash Reporting
  Notifications
  Dynamic Links
  Project Settings
  Docs
  Functions Log
  Functions Dashboard
</pre>

Select _Hosting: Deployed Site_ to see your live site in the browser. Then, experiment with some of the other options listed above to see what information Firebase can provide about your live-hosted site.

For more information about hosing and deployment with Firebase, check out the [Firebase Hosting Documentation](https://firebase.google.com/docs/hosting/).
