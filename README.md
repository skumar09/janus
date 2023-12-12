# Nala

<img width="600" alt="nala" src="https://user-images.githubusercontent.com/1972095/196048696-62bd0f4a-adf9-455f-bf12-29e6cc7a6290.png">

Automated E2E and integration testing of Milo-based projects.

## Getting started

## Scenario : I wanto to contribute to Nala framework and Milo Test Automation

### 1. Nala repositoy 

  - step-1 : Fork the Nala repository
    - Begin by forking the [Nala](https://github.com/adobecom/nala) repository
  - step-2 : Clone and Set Remote URLs
    - After forking, clone the repository to your local machine
    - Configure the remote URLs for both Upstream (original repository) and Origin (your fork).

### 2. Performing a Dry Run Test

    - Open the Nala codebase in Visual Studio Code (VSCode).
    - Execute the following sample command in the terminal to run the 'Quote' block tests
```bash
npx playwright test -g@quote
```
- If you encounter any errors, install the necessary dependencies as defined in the [`package.json`](https://github.com/adobecom/nala/blob/main/package.json), use the following commands:
```bash
 - npm install
 - npm fund 
```
- After installing the dependencies, re-run the above command to execute the Quote block test scripts. The tests should now run successfully.
 
### 3. Start Nala automation test script creation
Nala automation script creation involves following three simple steps.
- #### Step-1 : Create [`<block or feature name>.spec.js`](https://github.com/adobecom/nala/tree/main/features) under the `features` folder and add test cases and data
  - Please refer below sample template for creating a test case.
```bash
module.exports = {
  FeatureName: 'Quote Block',
  features: [
    {
      tcid: '0',
      name: '@Quote ',
      path: '/drafts/nala/blocks/quote/quote',
      data: {
        quoteCopy: '3D is a crucial part of how we explore the brand in a digital workflow',
        figCaption: 'Benny Lee',
        cite: 'Global Manager of Experiential Design, Coca-Cola Company',
      },
      tags: '@smoke @regression @milo,',
    },
  ],
}
```
<img width="1100" alt="nala spec" src="https://user-images.githubusercontent.com/22153717/245921315-f404404f-0b10-4735-a434-e329767f76aa.png">
----

- #### Step-2 : Create [`<block or feature name>.page.js`](https://github.com/adobecom/nala/tree/main/selectors) page object under the `selectors` folder and add locators
  - Please refer below sample template for creating a selector page object
```bash
export default class Quote {
  constructor(page) {
    this.page = page;
    // quote block locators
    this.quote = this.page.locator('.quote');
    this.quoteImage = this.quote.locator('.quote-image');
    this.quoteCopy = this.quote.locator('p.quote-copy');
    this.quoteFigCaption = this.quote.locator('p.figcaption');
    this.quoteFigCaptionCite = this.quote.locator('cite p');
    this.sectionDark = this.page.locator('.section.dark');
  }
}
```
<img width="1100" alt="nala pom" src="https://user-images.githubusercontent.com/22153717/245926484-e587de5b-6d66-4d0e-860d-c7a8a9d6fc26.png">
---

- #### Step-3 : Create [`<block or feature name>.test.js`](https://github.com/adobecom/nala-template/blob/main/tests/quote.block.test.js) under the `tests` folder, and add tests
  - Please refer sample template for creating tests. Also please refer [Nala onboarding wiki](https://github.com/adobecom/nala/wiki/Onboarding#tests)
```bash
// Quote block tests
test.describe('Milo Quote block test suite', () => {
  // before each test block
  test.beforeEach(async ({ page }) => {
    obj = new Quote(page);
    webutil = new WebUtil(page);
  });

  // Test - 0
  test(`${features[0].name},${features[0].tags}`, async ({ page, baseURL }) => {
    console.info(`${baseURL}${features[0].path}`);
    // test step-1
    await test.step('Go to Quote block test page', async () => {
      await page.goto(`${baseURL}${features[0].path}`);
      await page.waitForLoadState('domcontentloaded');
      await expect(page).toHaveURL(`${baseURL}${features[0].path}`);
    });

    // test step-2
    await test.step('Verify Quote block content / specs ', async () => {
      const { data } = features[0];
      
      await expect(page.locator('.quote')).toBeVisible();      

      await expect(await obj.quote).toBeVisible();
      await expect(await obj.quoteCopy).toContainText(data.quoteCopy);
      await expect(await obj.quoteFigCaption).toContainText(data.figCaption);

      // verify quote block css
      expect(await webutil.verifyCSS(
        await obj.quote,
        obj.cssProperties.quote,
      )).toBeTruthy();
    });
  });
});

```
<img width="1100" alt="nala pom" src="https://user-images.githubusercontent.com/22153717/245931730-f976d196-4074-4718-be51-c568d2365b45.png">
---

## 4 Running Nala tests
- Nala offers a range of flexible options to suit your testing needs. Please refer to the following choices for running your tests:
- By default Nala is configured to runs all tests in parallel in headless mode on following browsers 
  - Chrome
  - Firefox
  - WebKit
#### 4.1 : Run Everything
  - Example-1 : I want to run all tests on all environments or projects on all browsers in headless mode
```bash
npx playwright test
```
  - Example-2 : I want to run all tests on all environments or projects on all browsers in GUI mode
```bash
npx playwright test --headed
```
  - Example-3 : I want to run all tests on specific environments or projects (i.e [milo-live](https://main--milo--adobecom.hlx.live)) on chrome browser in headless mode
```bash
npx playwright test --project=milo-live-chrome
```
- Note : To run tests in GUI mode , you append `--headed` to run commands


#### 4.2 : Run Test Suite 
  - Example-1 : I want to run Quote block test suite on all environment or projects on all browsers in headless mode
```bash
npx playwright test quote.block.test
```
  - Example-2 : I want to run Quote block test suite on specific environment or projects (i.e [milo-live](https://main--milo--adobecom.hlx.live)) on firefox browsers in headless mode
```bash
npx playwright quote.block.test --project=milo-live-firefox
```

#### 4.3 : Run Tests using Tags (@) 
  ##### Example-1: I want to run all milo tests on all environment or projects on all browsers in headless mode
  - headless mode
```bash
npx playwright test -g@milo
``` 
  - Example-2: I want to run all smoke test suite on all environment or projects on all browsers in headless mode
```bash
npx playwright test -g@smoke
```
  - Example-3: I want to run all regression test suite on all environment or projects on all browsers in headless mode
```bash
npx playwright test -g@regression
```
  - Example-4: I want to run all quote block tests on all environment or projects on all browsers in headless mode
```bash
npx playwright test -g@quote
```
  - Example-5: I want to run quote and marquee blocks tests on all environment or projects on all browsers in headless mode
```bash
npx playwright test -g@quote|@marquee
```
  - Example-6: I want to run quote, marquee and accordion blocks tests on (i.e [milo-live](https://main--milo--adobecom.hlx.live)) envronment on chrome browser in headless mode
```bash
npx playwright test -g@quote|@marquee|@accordion --project=milo-live-chrome
```
- Note : To run tests using tags, make sure in `.spec.js` file `@tags` are specified 


### 5 : Run Tests on my localhost (i.e. 'http://localhost:3000',) 

  - To run Nala tests on your local host server, make sure you add or have following project object in [`playwright.config.js`](https://github.com/adobecom/nala/blob/main/playwright.config.js)
    ```bash
      {
        name: 'local-chrome',
        use: {
          ...devices['Desktop Chrome'],
          baseURL: envs['@local3000'],
        },
      },
    ```
##### Now, you are all set to run Nala tests on your local host
  - Example-1 : I want to run all tests on my local server or on project name = local-chrome on chrome browser in headless mode
```bash
npx playwright test --project=local-chrome
```
  - Example-2: I want to run all smoke test suite on my local server or on project name = local-chrome on chrome browser in headless mode
```bash
npx playwright test -g@smoke --project=local-chrome
```
- Note: Please refer above section-4, for various run options.


## 6 : Run tests on PRs
##### To run nala tests on pull requests (PRs) please following below labeling options, 

  - Example-1 : This PR affects Quote block functionality so i want to test Quote block tests on Milo 
```bash
  PR Label = @quote @run-nala 
```
- Example-2 : As part of this PR i want to verify all smoke tests on Milo 
```bash
  PR Label = @smoke @run-nala
```
Example-3 : As part of this PR i want to verify all regression tests on Milo 
```bash
  PR Label = @regression @run-nala
```
- Example-4 : As part of this PR i want to verify accordion and marquee block tests on Milo 
```bash
  PR Label = @accordion @marquee @run-on-milo
```
Example-5 : As part of this PR i want to verify smokes tests on Milo and Bcom applications 
```bash
  PR Label = @smoke @run-on-milo @run-on-bcom
```
- Note: PR should have label of the format :  `@tag(s) @run-on-<app name>`

## 7 : Daily Runs
##### All Milo and Consuming applications tests are scheduled to run between 6:00 to 9:30 AM PST 
  - Please refer [`<APP-Nala-Daily-Run.ymls`](https://github.com/adobecom/nala/blob/main/.github/workflows/) for daily run workflows 
  - Tests are run on following platerforms
    - Linux OS ( ubuntu-latests) with browsers = [Chrome, Firefox, and WebKit]
    - Windows OS ( windows-latests) with browsers = [Chrome, Firefox, and WebKit]
    - Mac OS ( macos-latests) with browsers = [Chrome, Firefox, and WebKit]

## 8: Nala wiki.

  - Please refer [Nala onboarding wiki](https://github.com/adobecom/nala/wiki/Onboarding) for more information