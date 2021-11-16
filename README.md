# cypress-and-jest-code-coverage

Cypress and Jest code coverage example.

## Setting up Next.js

- To get started, use the following command to create a Next.js project:

```bash
  npx create-next-app@latest
```

- We can open our Next.js app by running `yarn dev` which will start your app on http://localhost:3000.
- Let's add an about page by creating a `about.js` file under the pages folder with the following content.

```js
export default function About() {
  return (
    <div>
      <h1>About Page</h1>
    </div>
  )
}
```

- Now lets add a link to the `about page` from the homepage.
- Import Next.js Link component after the Next.js image omponent import.

```js
import Link from 'next/link'
```

- Now replace the `Get started by editing pages/index.js` code with a link to about page.

```jsx
<p className={styles.description}>
  Get started by editing{' '}
  <code className={styles.code}>pages/index.js</code>
</p>
```

> Code to be replaced.

```jsx
<Link href="/about">
  <a className={styles.description}>Go to about page</a>
</Link>
```

> Link to about page.

## Getting started with Cypress

- Install Cypress `yarn add cypress -D`
- Open Cypress `yarn run cypress open`
- Cypress will create some sample test files that demonstrate key Cypress concepts to help you get started. You can delete these by clicking on `No thanks, delete example files` in the Cypress app that opened with the previous command.
- So let's delete the sample test files by clicking on `No thanks, delete example files`.
- Add Cypress commands to the `scripts` field in `package.json` file.

```json
{
  "scripts": {
    "cypress:open": "cypress open"
  }
}
```

- Now you can invoke the command on your terminal from your project root like so:

```bash
yarn cypress:open
```

- Let's add a base URL to the `cypress.json` file, make sure this is the same url provided by your Next.js app.

```json
{
  "baseUrl": "http://localhost:3000"
}
```

- Add the following files/paths to your `.gitignore` file:
  - jest-coverage
  - .nyc_output
  - cypress-coverage/
  - cypress/videos
  - reports

## Creating your first Cypress integration test

- Add a test to check your navigation is working correctly:

```js
// cypress/integration/app.spec.js

describe('Navigation', () => {
  it('should navigate to the about page', () => {
    // Start from the index page
    cy.visit('http://localhost:3000/')

    // Find a link with an href attribute containing "about" and click it
    cy.get('a[href*="about"]').click()

    // The new url should include "/about"
    cy.url().should('include', '/about')

    // The new page should contain an h1 with "About page"
    cy.get('h1').contains('About Page')
  })
})
```

- You can use `cy.visit("/")` instead of `cy.visit("http://localhost:3000/")` if you added `"baseUrl": "http://localhost:3000"` to the `cypress.json` configuration file.

## Running your Cypress tests

Since Cypress is testing a real Next.js application, it requires the Next.js server to be running prior to starting Cypress. We recommend running your tests against your production code to more closely resemble how your application will behave.

Run `npm run build` and `npm run start`, then run `npm run cypress` in another terminal window to start Cypress.

> Note: Alternatively, you can install the `start-server-and-test` package and add it to the `package.json` scripts field: `"test": "start-server-and-test start http://localhost:3000 cypress:open"` or `"test": "start-server-and-test dev http://localhost:3000 cypress:open"` to start the Next.js production server in conjuction with Cypress. Remember to rebuild your application after new changes.

Running your Cypress test should work correctly.

## Adding Code coverage to Cypress

To handle code coverage collected during each test, we created a `@cypress/code-coverage` Cypress plugin. It merges coverage from each test and saves the combined result. It also calls nyc (its peer dependency) to generate static HTML reports for human consumption.
For more information on Cypress code coverage please read [Cypress Code Coverage Docs](https://docs.cypress.io/guides/tooling/code-coverage#Introduction).

> Please consult the @cypress/code-coverage documentation for up-to-date installation instructions.

- Install `@cypress/code-coverage`

```bash
yarn add @cypress/code-coverage -D
```

- Then add the code below to your [supportFile](https://docs.cypress.io/guides/references/configuration#Folders-Files) and [pluginsFile](https://docs.cypress.io/guides/references/configuration#Folders-Files).

```js
// cypress/support/index.js
import '@cypress/code-coverage/support'
```

- This plugin DOES NOT instrument your code. You have to instrument it yourself using [Istanbul.js](https://istanbul.js.org/) tool. Luckily it is not difficult. For example, if you are already using Babel to transpile you can add [babel-plugin-istanbul](https://github.com/istanbuljs/babel-plugin-istanbul) to your `.babelrc` and instrument on the fly.
- Install the plugin

```js
yarn add babel-plugin-istanbul -D
```

- Set your `.babelrc` file

```json
{
  "presets": ["next/babel"],
  "plugins": ["istanbul"]
}
```

- Put the following in `cypress/plugins/index.js` file to use `.babelrc` file

```js
// cypress/plugins/index.js
module.exports = (on, config) => {
  require('@cypress/code-coverage/task')(on, config)
  // include any other plugin code...

  // It's IMPORTANT to return the config object
  // with any changed environment variables
  return config
}
```

Now the code coverage from spec files will be combined with end-to-end coverage.

Find example of a just the unit tests and JavaScript source files with collected code coverage in [examples/unit-tests-js](https://github.com/cypress-io/code-coverage/blob/master/examples/unit-tests-js).

## CONVERT TO JEST?

- Before adding any tests, create `calc.js` file inside `src` with the following code:

```js
const add = (a, b) => a + b

const sub = (a, b) => a - b

module.exports = { add, sub }
```

- Remove the contents of `cypress/integration` and add `spec.js` file.
- In `cypress/integration/spec.js` let's require `sub` function and test it.

```js
// cypress/integration/spec.js
const { sub } = require('../../src/calc')

describe('Testing calc', () => {
  it('subtracts 10 - 5', () => {
    expect(sub(10, 5)).to.equal(5)
  })
})
```

- All tests should pass when running `yarn cypress:open` and clicking on `Run 1 integration spec`.
- Code coverage in Cypress is done via [@cypress/code-coverage](https://github.com/cypress-io/code-coverage) plugin. I suggest following the installation instructions in that repo.
  - Add @cypress/code-coverage by running `yarn add @cypress/code-coverage -D`
- d

## Sources

- [Installing Cypress](https://docs.cypress.io/guides/getting-started/installing-cypress#System-requirements)