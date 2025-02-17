---
title: "End-to-End Testing"
date: 2018-12-04T11:35:32-04:00
weight: 6
subsection: Web App
---

This page describes how to run End-to-End (E2E) testing and to build tests for a section or page of the Mattermost web application.  Under the hood, we are using [Cypress](https://www.cypress.io/) which is "fast, easy and reliable testing for anything that runs in a browser."

Not familiar with Cypress? Here is some documentation that will help you get started:

  - [Developer Guide](https://docs.cypress.io/guides/overview/why-cypress.html#In-a-nutshell)
  - [API Reference](https://docs.cypress.io/api/api/table-of-contents.html)

## Quick Overview on How to Run E2E Testing

1.  Run a local server instance by initiating `make run` to the mattermost-server folder. Confirm that the instance has started successfully.
   - Run `make test-data` to preload your server instance with initial seed data.  Generated data such as users are typically used for logging, etc.
   - Each test case should handle the required system or account settings, but in case you encountered some unexpected error while testing, you may want to run the server with default config based on `mattermost-server/config/default.json`).
2.  Change directory to `mattermost-webapp/e2e`, install npm dependencies by `npm i` and initiate `npm run cypress:run` in the command line. This will start full E2E testing. To run partial testing, you may initiate `npm run cypress:open` in the command line which will open its desktop app.  From there, you may select particular tests you would like to run.
3.  Tests are executed according to your selection and will display whether the tests passed or failed.

## Folder and File Structures

The folder structure is mostly based on the [Cypress scaffold](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests.html#Folder-Structure) which was created on initial run.  Folders and files are:
```
|-- e2e
  |-- cypress
    |-- fixtures
    |-- integration
    |-- plugins
    |-- support
    |-- utils
  |-- cypress.json
  |-- package-lock.json
  |-- package.json
```

- `/e2e/cypress/fixtures` or [Fixture Files](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests.html#Fixture-Files):
    - Fixtures are used as external pieces of static data that can be used by tests.
    - Typically used with the `cy.fixture()` command and most often when stubbing Network Requests.
- `/e2e/cypress/integration` or [Test Files](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests.html#Test-files)
    - To start writing tests,
        - simply create a new file (e.g. `login_spec.js`) within `/e2e/cypress/integration` folder and;
        - refresh tests list in the Cypress Test Runner and a new file should have appeared in the list.
- `/e2e/cypress/plugins` or [Plugin Files](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests.html#Plugin-files)
    - A convenience mechanism that automatically include the plugins before running every single spec file.
- `/e2e/cypress/support` or [Support Files](https://docs.cypress.io/guides/core-concepts/writing-and-organizing-tests.html#Support-file)
    - The support file is a place to put reusable behaviour such as Custom Commands or global overrides that are wished to be applied and available to all of spec files.
- `/e2e/cypress/utils`
    - A place for common utility functions
- `/e2e/cypress.json`
    - Use to store Cypress [configuration](https://docs.cypress.io/guides/references/configuration.html#Options)
- `/e2e/package.json`
    - Use to store all dependencies related to Cypress end-to-end testing

## What requires an E2E Test?

1. Test cases defined in help-wanted E2E issues, which are drawn from the release testing list
2. New features or stories
3. Bug fixes
4. Cases that are not covered by unit or integration tests

## Interested in Contributing to E2E Testing through Help Wanted Tickets

1. All help wanted tickets are under [server repository's GitHub issues](https://mattermost.com/pl/help-wanted-mattermost-server). Look for issues with `Add E2E Tests` and `Up For Grabs` labels, and comment to let everyone know you're working on it.
2. Each ticket is filled up with specific test steps and verifications that need to be accomplished as a minimum requirement.  Additional steps and assertions for robust test implementation are much welcome.
3. Join our channel at [UI Test Automation](https://community.mattermost.com/core/channels/ui-test-automation) and talk to us as fellow contributors, and collaborate and learn with one another.

## Guide for Writing E2E Testing

1. The [recommended practice](https://docs.cypress.io/guides/references/best-practices.html#Selecting-Elements) of Cypress is to use `data-*` attribute to provide context to selectors, but we prefer to use element ID instead.
   - If element ID is not present in the webapp, you may add it in `camelCase` form with human readable name (e.g. `<div id='sidebarTitle'>`). Watch out for potential breaking changes in the snapshot of the unit testing.  Run `make test` to see if all are passing, and run `npm run updatesnapshot` or `npm run test -- -u` if necessary to update snapshot testing.
2. Add commands or shortcuts to `/e2e/cypress/support/commands.js` (e.g. `toAccountSettingsModal`) that makes it easier to access a page, section, modal and etc. by simply using it as `cy.toAccountSettingsModal('user-1')`.
3. Organize `/e2e/cypress/integration` with a subfolder to group similar tests.
4. This is used to track test cases in our core staff Release Testing spreadsheet, which is linked in the header of the [Release Discussion channel](https://community.mattermost.com/core/channels/release-discussion) during release testing.

    In the spec file, it should be written as:

    ```javascript
    describe('Emoji reaction', () => {
        it('M14014 Recently used emojis are shown 1st', () => {
            // Test steps and assertion here
        }
    }
    ```

    Where `"M14014"` is the test key and `"Recently used emojis are shown 1st"` is the test description.  This information is stated in Github's [help-wanted ticket](https://github.com/mattermost/mattermost-server/issues/10246) and [Jira ticket](https://mattermost.atlassian.net/browse/MM-14014).

    The naming convention used is the name of the tab on the release testing spreadsheet e.g. AS = Account Settings; M = Messaging, etc. and the numerical value is from Jira ticket identifier. So `"M14014"` means "Messaging" on release testing spreadsheet with `MM-14014` Jira ticket.

1. Refer to [this pull request](https://github.com/mattermost/mattermost-webapp/pull/2058/files#diff-c42a18e742b351c0ade058ed0c4b5c5eR10) as a guide on how to write and submit an end-to-end testing PR.

## Troubleshooting

### Cypress Failed To Start After Running 'npm run cypress:run'

Either the command line exits immediately without running any test or it logs out like the following.

##### Error message
```sh
✖  Verifying Cypress can run /Users/user/Library/Caches/Cypress/3.1.3/Cypress.app
   → Cypress Version: 3.1.3
Cypress failed to start.

This is usually caused by a missing library or dependency.
```

##### Solution
Clear node options by initiating `unset NODE_OPTIONS` in the command line. Running `npm run cypress:run` should proceed with Cypress testing.


##### Error message
This error may occur in Ubuntu when running any Cypress spec.

```
code: 'ENOSPC',
errno: 'ENOSPC',
```
##### Solution
Run the following command

```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```
