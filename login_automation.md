# LOGIN AUTOMATION

### STEP 1
- Open **cypress.config.js**
- Copy and paste the following code
```
const { defineConfig } = require('cypress')

module.exports = defineConfig({
    e2e: {
        baseUrl: 'https://portal-selgdx.selangor.gov.my',
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
        "email": "admin@cloud-connect.asia",
        "password": "Password123"
    },
    "invalid": {
        "email": "admin#cloud-connect.asia",
        "password": "pass" 
    },
    "empty": {
        "email": "",
        "password": ""
    },
    "sqlInjection": {
        "email": "' OR '1'='1",
        "password": "' OR '1'='1"
    },
    "xss": {
        "email": "<script>alert(1)</script>",
        "password": "Password123"
    }
}
```

### STEP 3
- Open **support/commands.js**
- Copy and paste the following code
```
Cypress.Commands.add('loginWith', (email, password) => { 
    if (email !== undefined) cy.get('[data-cy="email"]').clear().type(email)
    if (password !== undefined) cy.get('[data-cy="password"]').clear().type(password)
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

    context('Positive Scenarios', () => {
        it('TC01 - Login with valid data', () => {
            cy.loginWith(loginData.valid.email, loginData.valid.password)
            cy.url().should('include', '/dashboard')
            cy.contains(loginData.welcome).should('be.visible')
            cy.logout()
        })
    })

    context('Negative Scenarios', () => {
        it('TC02 - Login with invalid data', () => {
            cy.loginWith(loginData.invalid.email, loginData.invalid.password)
            cy.contains('Email and password is invalid').should('be.visible')
        })
    
        it('TC03 - Login with empty data', () => {
            cy.loginWith(loginData.empty.email, loginData.empty.password)
            cy.contains('Email is required').should('be.visible')
            cy.contains('Password is required').should('be.visible')
        })
    
        it('TC04 - Login with XSS in email', () => {
            cy.loginWith(loginData.xss.email, loginData.xss.password)
            cy.contains('Invalid email or password').should('be.visible')
        })
    
        it('TC05 - Login with SQL Injection', () => {
            cy.loginWith(loginData.sqlInjection.email, loginData.sqlInjection.password)
    
            // App should NOT crash or login successfully
            cy.contains('Invalid email or password').should('be.visible')
            cy.url().should('include', '/login')
        })
    })
})
```

