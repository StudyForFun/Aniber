# Aniber
> Aniber is an e2e testing tool presets powered by Selenium and Nightwatch.
> © 2016 LancerComet.

## Introduction
is an e2e testing tool presets powered by Selenium and Nightwatch.
It's standalone and you can test anything you want to any site.

![](https://raw.githubusercontent.com/LancerComet/Aniber/develop/preview.gif)


## Get started

### Setup.
1. `git clone`.
2. Run `npm install` to get all dependencies.
3. Run `npm run selenium-setup` to install selenium.


### Write testing cases.
1. Make a new **.js** file and save it to folder **./tests**. You can name this file what you want.
2. Write testing code.
> You may want to check the [documents](http://nightwatchjs.org/) about how to write testing case and using APIs.

  Here is an example.
  ```
  const accountConfig = {
      username: 'username',
      password: 'password',
      uid: 10000
  }
  module.exports = {  
      'Bilibili Live Login Test': function (client) {
        client.url('http://live.bilibili.com').maximizeWindow()

        // Page Init.
        client.expect.element('body').to.be.present.before(3000)
        client.expect.element('.top-nav-login-btn.last').to.be.visible

        // Login.
        client.click('.top-nav-login-btn.last')
        client.waitForElementVisible('#bilibili-quick-login', 2000)
        client.frame(0)
        client.pause(2000)
        client.setValue('#login-username', accountConfig.username)
        client.setValue('#login-passwd', accountConfig.password)
        client.click('#login-submit')

        // Wait and check page has been reloaded.
        client.frameParent()
        client.pause(3000)
        client.expect.element('body').to.be.present.before(3000)

        // Check cookies to ensure we are signed in.
        client.getCookies(function (result) {
          result.value.forEach((value, index, array) => {
            if (value.name === 'DedeUserID') client.assert.equal(parseInt(value.value, 10), accountConfig.uid)
          })
        })

        // Move to User Avatar.
        client.expect.element('.user-avatar-link').to.be.visible
        client.moveToElement('.user-avatar-link', 5, 5)
        client.pause(500)
        client.expect.element('#top-nav-user-panel').to.be.visible

        // Logout.
        client.click('#top-nav-logout-link')
        client.pause(5000)
        client.expect.element('body').to.be.present.before(3000)

        // Check cookies to make sure we are off.
        client.getCookies(function (result) {
          var logout = true
          result.value.forEach((value, index, array) => {
            if (value.name === 'LIVE_LOGIN_DATA') logout = false
          })
          client.assert.equal(logout, true)
        })

        client.pause(1000)
        client.end()
      }
    }
  ```

### Run your test.
Run `npm start` to run your testing cases.

## NPM Scripts
 - `npm start`: Run all testing cases.
 - `npm run selenium-setup`: Install Selenium to your project.
 - `npm run selenium-start`: Startup Selenium manually.

## File Structure
```
>
|-  build                     
|     |- selenium-conf.js     # Selenium and Driver configuration.
|     |- selenium-setup.js    # Selenium setup function.
|     └- selenium-start.js    # Selenium startup function.
|
|-  reports                   # Testing report.
|-  tests                     # Testing cases.
|
|-  nightwatch.json           # Nightwatch configuration file.
|-  nightwatch.conf.js        # Nightwatch configuration file.
|-  package.json              # Project configuration.
|-  Readme.md                 # Introduction.
└-  startup.js                # Entry file, to start app.
```