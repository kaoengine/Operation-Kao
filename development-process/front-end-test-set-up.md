# Project Setup
In this chapter, we'll set up our project and process. This won't involve writing any app-specific functionality. Instead, what we'll do is:

- Make a list of our stories to work on
- Get our development machine dependencies installed
- Get React installed and running
- Get linting and autoformatting working
- Get E2E and component tests running
- Get our tests running on CI
- Get our application automatically deploying to a hosting service

That's a lot, but we want to get it in place before we write our first line of production code to ensure we have support in place for our agile process. And it won't take too long because we'll use powerful tools to help us get there. Once that's all in place, we'll be ready to start implementing our application's features.

# Making a List of Stories
Agile developers often use the term "stories" to refer to units of work. This term emphasizes the fact that agile developers stay focused on delivering units of work that are useful to the user. Rather than "building out the data model" (which would not make for a very compelling tale!), a story would be more likely to be something like "users can see a list of restaurants they've entered."

Before we start our work, we want a way to track it. In this guide it'll just be you working by yourself, so you could just keep a to-do list on paper or a to-do app if you like. But when doing agile development in a team environment, it's a good idea to have a shared tracker. Trello is a great flexible tool that is useful for tracking work.

If you want to try out Trello as part of this guide, go to https://trello.com (opens new window)and sign up for a free account. Create a new board and name it "Agile Frontend Dev". Create three columns: "To Do", "In Progress", and "Done". In the "To Do" column, add a card for each of the following stories:

- Set Up Development Environment
- Create App
- Set Up Autoformatting
- Set Up Tests On CI
- Set Up Automatic Deployment
- Fill In Readme
- List Restaurants
- Style App with Material Design
- Show Loading and Error States
- Add Restaurants

# Setting Up Development Environment
Our first task is "Set Up Development Environment". In Trello, drag that card to the "In Progress" column.

Here are the tools we'll need to install:

- Git
- Node
- Yarn
- An editor
## Git
Version control is essential for most developers, but even more so for agile developers. You need to be able to track the small steps you take to make sure they aren't lost. Although we won't get into it in this guide, focused and well-explained commit messages are essential for communicating to your teammates as well. Git (opens new window)is probably the most popular version control tool right now, and we'll use GitHub for pull requests and CI purposes as well.

## Node
We'll be using Create React App as our build tool. Like most frontend build tools, it runs on top of Node.js (opens new window), so you'll need node installed locally.

## Yarn
Yarn (opens new window)is an alternative npm client. Generally I find it to be faster, more predictable, and more reliable than using the default npm client. I use yarn for all my projects.

If you'd prefer to use npm, you can still follow this guide, you'll just need to replace any yarn commands with the equivalent npm commands.

## An Editor
There are a number of different editors that are good for React development; two popular free ones are Visual Studio Code (opens new window)and Atom (opens new window).

With this, we can drag the "Set Up Development Environment" story to the "Done" column in Trello.

# Creating the App
Our next story is "Create App"; drag it to "In Progress".

Create a new React app:
```
    $ npx create-react-app opinion-ate
```
Create React App will start the installation process, and when it completes, your application will be created and ready to use.

Open your package.json and note the scripts we have available:

- **start** to run the app locally
- **build** to create a release build of the app
- **test** to run unit tests, including React component tests
- **eject** for if we ever want to move away from Create React App

Let's try it out. Run yarn start. Your app should automatically open in your default browser, with a spinning React logo.

React app intro screen

In the console you'll see something like the following:
```
    Compiled successfully!

    You can now view opinion-ate in the browser.

    Local:            http://localhost:3000
    On Your Network:  http://10.0.1.7:3000

```

Note that the development build is not optimized.
To create a production build, use yarn build.
With this, we can move our "Create App" task in Trello to "Done".

# Setting Up Autoformatting
Next is "Set Up Autoformatting"; drag it to "In Progress".

Create React App includes a built-in ESLint config to check your code for issues while it runs. But we can set up a separate ESLint config that our editor can see, so it can warn us about issues right away and autoformat files upon save.

Add this rather lengthy list of packages:
```
$ yarn add --dev eslint-config-prettier \
                 eslint-plugin-cypress \
                 eslint-plugin-jest \
                 eslint-plugin-prettier \
                 prettier
```
Then create a file **.eslintrc.js** at the root of your project and add the following:

```
module.exports = {
  extends: ['react-app', 'prettier'],
  plugins: ['prettier', 'jest', 'cypress'],
  parser: 'babel-eslint',
  env: {
    browser: true,
    'cypress/globals': true,
    es6: true,
    'jest/globals': true,
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
  rules: {
    'prettier/prettier': 'warn',
  },
};
```
Here's what this file is configuring:

Sets up with Create React App's default linting rules, adding in Prettier for autoformatting.
Makes ESLint aware of global variables provided by the ECMAScript 6 version of the language, the browser, Jest, and Cypress.
Next, let's tweak the autoformatting setup. Prettier doesn't have a lot of configuration options, but you can adjust a little.

And another file **.prettierrc.js** and add the following:
```
module.exports = {
  arrowParens: 'avoid',
  bracketSpacing: false,
  singleQuote: true,
  trailingComma: 'all',
};
```

These options configure Prettier with some helpful options to match common JavaScript practice. It also makes our diffs a bit simpler to read, which is not only helpful for this guide, but is also helpful for code reviews in your own projects.

Now, set up ESLint integration with your editor. For example:

    * Atom: linter-eslint(opens new window)
    * VS Code: ESLint extension(opens new window)

After enabling this integration, restart your editor to make sure it picks up the latest config changes. Then open App.test.js. Notice the warning on line 1 inside the curly brackets: Prettier is suggesting removing the spaces inside the curlies. If you've enabled autoformatting on save, which I recommend, when you save the file those spaces will be removed automatically.

To make sure our app builds correctly, we need to make sure there are no lint warnings in any of the default JavaScript files created in our app. Create React App will let the app run locally with warnings, which is helpful as you work. But it will fail the production build if there are any lint warnings, which is good to make sure we've handed any code style issues.

To help find and correct any lint issues in our app, let's set up an npm script to run the lint command. Add the following to package.json:

```
 "scripts": {
   "start": "react-scripts start",
   "build": "react-scripts build",
   "test": "react-scripts test",
+  "lint": "eslint src",
   "eject": "react-scripts eject"
 }
```


This command will run ESLint on all JavaScript files in our src/ directory.

Let's try it. Run yarn lint. Depending on your version of Create React App you will likely get a few warnings. Here's what I got:

```
$ yarn lint
yarn run v1.22.10
$ eslint src

/Users/josh/apps/opinion-ate/src/index.js
  11:34  warning  Insert `,`  prettier/prettier

/Users/josh/apps/opinion-ate/src/reportWebVitals.js
  3:33  warning  Replace `·getCLS,·getFID,·getFCP,·getLCP,·getTTFB·` with
  `getCLS,·getFID,·getFCP,·getLCP,·getTTFB`  prettier/prettier

✖ 2 problems (0 errors, 2 warnings)
  0 errors and 2 warnings potentially fixable with the `--fix` option.

✨  Done in 1.55s.
```

Notice the message that says "2 warnings potentially fixable with the --fix option". We saw autofix functionality earlier when we saved our file in the editor. The --fix flag is how to use this functionality from the command line.

Run yarn lint --fix. If all the warnings you got were autofixable, the command should complete without any warnings. Run git status and you should see all the files that you got warnings about have changes applied.

Let's go ahead and commit these configuration changes to linting and formatting. Small, focused commits make it easier for other developers to review, and keep us accountable to really understanding what is changing in our code. Create React App initializes our app with a git repo, so we can just add the changes:

```
$ git add .
$ git commit -m "Set up linting and autoformatting"
```

With this, we can drag "Set Up Autoformatting" to "Done".

# Running Tests on CI
Next is "Set Up Tests on CI"; drag it to "In Progress".

Create React App automatically sets up an example component test for us. Before we run it on CI, let's confirm it works for us locally. Open src/App.test.js. Note that it's testing the App component. Now run yarn test. You may get a note "No tests found related to files changed since last commit." If so, press the "A" key to run all tests.

You should see the following:
```
PASS  src/App.test.js
 ✓ renders learn react link (32ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.816s
Ran all test suites.

Watch Usage: Press w to show more.
Press ctrl-C to leave the test runner.
```

Now we need to install Cypress for end-to-end tests. Run:
```
$ yarn add --dev cypress
```

When it completes, in your package.json, add a new script:
```
 "scripts": {
   "start": "react-scripts start",
   "build": "react-scripts build",
   "test": "react-scripts test",
+  "cypress": "cypress open",
   "lint": "eslint src",
   "eject": "react-scripts eject"
 }
 ```

Now run that command:
```
$ yarn cypress
```

A Cypress window will open, with a modal that says "To help you get started", informing us Cypress created some sample files. Click "OK, got it!"

Now let's tweak these files. In the root of your project should be a cypress.json file. Open it and replace the contents with:

This configures Cypress to interpret all relative URLs relative to the root of our local React app.

Now, open the cypress/integration folder Cypress created, and see that there is an examples child folder under it. Delete the examples folder with its child test files. In its place, create a file cypress/integration/smoke.spec.js and add the following contents:
```
describe('Smoke Test', () => {
  it('can view the home page', () => {
    cy.visit('/');
    cy.contains('Learn React');
  });
});
```
This test will load up the root of our app and confirm it can see the text "Learn React" on it.

Now, back in the Cypress window, you should see the list updated to only contain our smoke.spec.js test

Click on smoke.spec.js. A new instance of Chrome will open and you'll see the Cypress test runner interface. On the left is our "Smoke Test" and a series of steps, which should pass. On the right is our app.

Cypress window with smoke test

Add the new Cypress files to git:
```
$ git add .
$ git commit -m "Set up Cypress E2E tests"
```
When Create React App creates our project, it initializes a git repo and adds our code to it. Let's push it up to GitHub. On github.com (opens new window), create a new GitHub repo.

Next, add it as the origin remote and push up the repo:
```
$ git remote add origin https://github.com/your-user-name/your-repo-name.git
$ git push --set-upstream origin main
```

In many development approaches, the next thing we would do would be to start building application functionality. After that, we might release to production. Later we might decide to try to add testing and continuous integration.

But we're going to go the opposite route in this guide. We're going to set up CI and deployment from the very start, before we write a line of production code.

There are a number of great CI services with free starter plans, including CircleCI (opens new window)and GitHub Actions (opens new window). We're going to go with GitHub Actions due to the fact that every GitHub repo is set up for Actions automatically.

When we start our feature work we'll do it in branches. Let's go ahead and configure GitHub Actions in a branch as well, to get used to the workflow. Create a new ci branch:
```
$ git checkout -b ci
```