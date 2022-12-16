# Alph Browser Extension


Alph-wallet-extension supports Firefox, Google Chrome, and Chromium-based browsers. We recommend using the latest available browser version.


## Building locally

- Install [Node.js](https://nodejs.org) version 16
    - If you are using [nvm](https://github.com/nvm-sh/nvm#installing-and-updating) (recommended) running `nvm use` will automatically choose the right node version for you.
- Install [Yarn v3](https://yarnpkg.com/getting-started/install)
- Install dependencies: `yarn`
    - Optionally, replace the `PASSWORD` value with your development wallet password to avoid entering it each time you open the app.
- Build the project to the `./dist/` folder with `yarn dist`.
    - Optionally, you may run `yarn start` to run dev mode.

Uncompressed builds can be found in `/dist`, compressed builds can be found in `/builds` once they're built.

See the [build system readme](./development/build/README.md) for build system usage information.

## Contributing

### Development builds

To start a development build (e.g. with logging and file watching) run `yarn start`.

#### React and Redux DevTools

To start the [React DevTools](https://github.com/facebook/react-devtools), run `yarn devtools:react` with a development build installed in a browser. This will open in a separate window; no browser extension is required.

To start the [Redux DevTools Extension](https://github.com/reduxjs/redux-devtools/tree/main/extension):
- Install the package `remotedev-server` globally (e.g. `yarn global add remotedev-server`)
- Install the Redux Devtools extension.
- Open the Redux DevTools extension and check the "Use custom (local) server" checkbox in the Remote DevTools Settings, using the default server configuration (host `localhost`, port `8000`, secure connection checkbox unchecked).

To create a development build and run both of these tools simultaneously, run `yarn start:dev`.


### Running Unit Tests and Linting

Run unit tests and the linter with `yarn test`. To run just unit tests, run `yarn test:unit`.

You can run the linter by itself with `yarn lint`, and you can automatically fix some lint problems with `yarn lint:fix`. You can also run these two commands just on your local changes to save time with `yarn lint:changed` and `yarn lint:changed:fix` respectively.

### Running E2E Tests

Our e2e test suite can be run on either Firefox or Chrome.

1. **required** `yarn build:test` to create a test build.
2. run tests, targetting the browser:
 * Firefox e2e tests can be run with `yarn test:e2e:firefox`.
 * Chrome e2e tests can be run with `yarn test:e2e:chrome`. The `chromedriver` package major version must match the major version of your local Chrome installation. If they don't match, update whichever is behind before running Chrome e2e tests.

These test scripts all support additional options, which might be helpful for debugging. Run the script with the flag `--help` to see all options.

#### Running a single e2e test

Single e2e tests can be run with `yarn test:e2e:single test/e2e/tests/TEST_NAME.spec.js` along with the options below.

```console
  --browser        Set the browser used; either 'chrome' or 'firefox'.
                                         [string] [choices: "chrome", "firefox"]
  --debug          Run tests in debug mode, logging each driver interaction
                                                      [boolean] [default: false]
  --retries        Set how many times the test should be retried upon failure.
                                                           [number] [default: 0]
  --leave-running  Leaves the browser running after a test fails, along with
                   anything else that the test used (ganache, the test dapp,
                   etc.)                              [boolean] [default: false]
```

For example, to run the `account-details` tests using Chrome, with debug logging and with the browser set to remain open upon failure, you would use:
`yarn test:e2e:single test/e2e/tests/account-details.spec.js --browser=chrome --debug --leave-running`

### Changing dependencies

Whenever you change dependencies (adding, removing, or updating, either in `package.json` or `yarn.lock`), there are various files that must be kept up-to-date.

* `yarn.lock`:
  * Run `yarn` again after your changes to ensure `yarn.lock` has been properly updated.
  * Run `yarn lint:lockfile:dedupe:fix` to remove duplicate dependencies from the lockfile.
* The `allow-scripts` configuration in `package.json`
  * Run `yarn allow-scripts auto` to update the `allow-scripts` configuration automatically. This config determines whether the package's install/postinstall scripts are allowed to run. Review each new package to determine whether the install script needs to run or not, testing if necessary.
  * Unfortunately, `yarn allow-scripts auto` will behave inconsistently on different platforms. macOS and Windows users may see extraneous changes relating to optional dependencies.
* The LavaMoat policy files. The _tl;dr_ is to run `yarn lavamoat:auto` to update these files, but there can be devils in the details:
  * There are two sets of LavaMoat policy files:
    * The production LavaMoat policy files (`lavamoat/browserify/*/policy.json`), which are re-generated using `yarn lavamoat:background:auto`. Add `--help` for usage.
      * These should be regenerated whenever the production dependencies for the background change.
    * The build system LavaMoat policy file (`lavamoat/build-system/policy.json`), which is re-generated using `yarn lavamoat:build:auto`.
      * This should be regenerated whenever the dependencies used by the build system itself change.
  * Whenever you regenerate a policy file, review the changes to determine whether the access granted to each package seems appropriate.
  * Unfortunately, `yarn lavamoat:auto` will behave inconsistently on different platforms.
  macOS and Windows users may see extraneous changes relating to optional dependencies.
  * If you keep getting policy failures even after regenerating the policy files, try regenerating the policies after a clean install by doing:
    * `rm -rf node_modules/ && yarn && yarn lavamoat:auto`
  * Keep in mind that any kind of dynamic import or dynamic use of globals may elude LavaMoat's static analysis.
  Refer to the LavaMoat documentation or ask for help if you run into any issues.

## Architecture

- [Visual of the controller hierarchy and dependencies as of summer 2022.](https://gist.github.com/rekmarks/8dba6306695dcd44967cce4b6a94ae33)

[![Architecture Diagram](./docs/architecture.png)][1]

## Other Docs

- [How to add custom build to Chrome](./docs/add-to-chrome.md)
- [How to add custom build to Firefox](./docs/add-to-firefox.md)
- [Publishing Guide](./docs/publishing.md)
- [How to use the TREZOR emulator](./docs/trezor-emulator.md)
- [How to generate a visualization of this repository's development](./development/gource-viz.sh)

