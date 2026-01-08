# LOGIN AUTOMATION

### STEP 1
- Open **cypress.config.js**
- Copy and paste the following code
```
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  e2e: {
    baseUrl: 'YOUR_WEBSITE_HERE',
    viewportWidth: 1280,
    viewportHeight: 800,
    video: true,
    screenshotOnRunFailure: true,
    defaultCommandTimeout: 8000,
    setupNodeEvents(on, config) {
      // node events here
    },
  },
})
```

### STEP 2
- Create **fixtures/loginData.json**
- Copy and paste the following code
```
{
    "welcome": "Welcome to Cypress",
    "valid": {
        "email": "admin@mail.com",
        "password": "admin123"
    },
    "invalid": {
        "email": "invadmin@mail.com",
        "password": "invadmin123"
    }
}
```

### STEP 3
- Open **support/commands.js**
- Copy and paste the following code
```
Cypress.Commands.add('loginWith', (email, password) => { 
    cy.get('[data-cy="email"]').type(email)
    cy.get('[data-cy="password"]').type(password)
    cy.get('[data-cy="login"]').click()
})

Cypress.Commands.add('logout', () => {
    cy.get('[data-cy="logout"]').click()
})
```

### STEP 4
- Create **e2e/auth/login.cy.js**
- Copy and paste the following code
```
/// <reference types="cypress" />

import loginData from '../../fixtures/loginData.json'

describe('TS01 - Manage Login', () => {
    beforeEach(() => {
        cy.visit('/login')
    })

    context('Valid data', () => {
        it('TC01 - Login with valid data', () => {
            cy.loginWith(loginData.valid.email, loginData.valid.password)
            cy.url().should('include', '/')
            cy.contains(loginData.welcome).should('be.visible')
            cy.logout()
        })
    })

    context('Invalid data', () => {
        it('TC02 - Login with invalid data', () => {
            cy.loginWith(loginData.invalid.email, loginData.invalid.password)
            cy.contains('Email and password is invalid').should('be.visible')
        })
    })
})

```
