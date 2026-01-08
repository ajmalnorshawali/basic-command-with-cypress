# REGISTRATION AUTOMATION

### STEP 1
- Open **cypress.config.js**
- Copy and paste the following code
```
const { defineConfig } = require('cypress')

module.exports = defineConfig({
    e2e: {
        baseUrl: 'YOUR_URL_HERE',
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
- Create **fixtures/regData.json**
- Copy and paste the following code
```
{
    "valid": {
        "username": "Admin",
        "email": "admin@cloud-connect.asia",
        "password": "Password123"
    },
    "invalid": {
        "username": "Admin!",
        "email": "admin#cloud-connect.asia",
        "password": "pass"
    },
    "empty": {
        "username": "",
        "email": "",
        "password": ""
    },
    "xss": {
        "username": "<script>alert(1)</script>",
        "email": "<script>alert(1)</script>@test.com",
        "password": "Password123"
    },
    "sqlInjection": {
        "username": "' OR '1'='1",
        "email": "' OR '1'='1",
        "password": "' OR '1'='1"
    }
}
```

### STEP 3
- Open **support/commands.js**
- Copy and paste the following code
```
Cypress.Commands.add('registrationWith', (username, email, password) => {
    if (username !== undefined) cy.get('[data-cy="username"]').clear().type(username)
    if (email !== undefined) cy.get('[data-cy="email"]').clear().type(email)
    if (password !== undefined) cy.get('[data-cy="password"]').clear().type(password)
    
    cy.get('[data-cy="register"]').click()
})
```

### STEP 4
- Create **e2e/registration.cy.js**
- Copy and paste the following code
```
/// <reference types="cypress" />
import regData from '../../fixtures/regData.json'

describe('TS01 - Manage Registration', () => {

    beforeEach(() => {
        cy.visit('/register')
    })

    context('Positive Scenarios', () => {
        it('TC01 - Register with valid data', () => {
            cy.registrationWith(
                regData.valid.username,
                regData.valid.email,
                regData.valid.password
            )

            cy.contains('Registration successful').should('be.visible')
        })
    })

    context('Negative Scenarios', () => {
        it('TC02 - Register with invalid data', () => {
            cy.registrationWith(
                regData.invalid.username,
                regData.invalid.email,
                regData.invalid.password
            )

            cy.contains('Invalid email or password').should('be.visible')
        })

        it('TC03 - Register with empty data', () => {
            cy.registrationWith(
                regData.empty.username,
                regData.empty.email,
                regData.empty.password
            )

            cy.contains('Username is required').should('be.visible')
            cy.contains('Email is required').should('be.visible')
            cy.contains('Password is required').should('be.visible')
        })

        it('TC04 - Register with XSS payload', () => {
            cy.registrationWith(
                regData.xss.username,
                regData.xss.email,
                regData.xss.password
            )

            cy.contains('Invalid input').should('be.visible')
            cy.url().should('include', '/register')
        })

        it('TC05 - Register with SQL Injection payload', () => {
            cy.registrationWith(
                regData.sqlInjection.username,
                regData.sqlInjection.email,
                regData.sqlInjection.password
            )

            cy.contains('Invalid input').should('be.visible')
            cy.url().should('include', '/register')
        })
    })
})
```
