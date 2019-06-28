# MetaDefender browser extension

## Intro

OPSWAT File Security for Chrome, provides the ability to quickly scan a file for malware prior to download directly from the browser. At the moment this only works with google chrome, but can be extended to any other browser.

## Using `mocli`

`mocli` is a command line tool that helps performing project setup and some development task automation.
You can find the source code in `./mocli.sh` file.

For proper usage we suggest installing [autoenv](https://github.com/kennethreitz/autoenv) to execute the `./.env` file. As an alternative you can run the env file `source ./.env` manually.
Once executed, the `mocli` command will be available inside the project folder.
Run `mocli help` to get a list of available commands.

## Project Setup

Install dependencies and configure environment:

```bash
mocli developer
```

_You may omit the next step if you don't need GA tracking._
Update the `./secret.json` file in the project root folder with your own Google Analytics tracking code.

```json
{
    "googleAnalyticsId": "<your-ga-id>"
}
```

## Development

```bash
# Start livereload and watches all assets
mocli watch

# Start release branch and create a qa distribution
mocli release start <version>

# Close release branch and create a prod distribution
mocli release finish <version>
```

After running `mocli watch` [load the extension in your browser](https://developer.chrome.com/extensions/getstarted#manifest) from the `dist/<vendor>` directory.

### Folder structure

`./app/_locales/` extension translation files.

`./app/config/` environment configuration files. `common.json` is the main config file. `/scripts/common/config.js` file is generated by overwriting `common.json` with an env specific json file.

`./app/fonts/` extension font files.

`./app/html/` angular template files. Partial templates are placed in a subdirectory with the same name as the main template file.

`./app/images/` extension assets.

`./app/scripts/` Javascript files. Two angular modules: `popup.js` and `extension.js`. Subfolders with the same name as the module contain module related files. `common/angular` - angular modules. `common/browser` - browser API wrapers. `common/persistent` - persistent objects, saved in browser storage.

`./app/styles/` extension style files.

### Entryfiles (bundles)

There are two kinds of entryfiles that create bundles.

1. All js-files in the root of the `./app/scripts/` directory.
2. All css-, scss- and less- files in the root of the `./app/styles/` directory.

## Gulp Tasks

### Config

```bash
gulp config --[prod|qa|local|dev]
```

| Option            | Description                                                                                                                                           |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--prod, --qa, --local, --dev` | Overwrite common configuration file with prod, dev, qa or local. Configuration files location  `/app/config/` |

### Build

```bash
gulp [OPTIONS]
```

| Option            | Description                                                                                                                                           |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--watch`         | Starts a livereload server and watches all assets. To reload the extension on change include `livereload.js` in your bundle.                          |
| `--production`    | Minifies all assets                                                                                                                                   |
| `--verbose`       | Log additional data to the console.                                                                                                                   |
| `--vendor`        | Compile the extension for different vendors (chrome, firefox, opera, edge)  Default: chrome                                                           |
| `--sourcemaps`    | Force the creation of sourcemaps. Default: !production                                                                                                |
| `--devbuild`      | Disable chromereload when not building for production. For testing on other computers.

### Pack

Zips your `dist` directory and saves it in the `packages` directory.
`build` command options apply.
Use `--devbuild` if not building for production.
Use `--env=qa|dev|prod|local` to add environment info to generated package name.

```bash
gulp pack [--production] [--vendor=chrome] [--env=qa]
```

### Version

`@deprecated` in favor of git flow workflows.

Increments version number of `manifest.json` and `package.json`, commits the change to git and adds a git tag. We use semantic versioning.

```bash
gulp patch      // => 0.0.X @deprecated use `git flow hotfix`
# or
gulp feature    // => 0.X.0 @deprecated use `git flow release`
# or
gulp release    // => X.0.0 @deprecated use `git flow release`
```

## Globals

The build tool creates a config file in `./app/scripts/config.js` from `./app/config/common.json`.
If the build is for development `./app/config/dev.json` is used to overwrite `common.json` data.
`config.js` creates a `MCL.config` global variable.

**Example:** `./app/extension.js`

```javascript
import './config';

if (MCL.config.env === 'dev') {
    console.log(MCL.config);
}
```

The build tool also defines a variable named `process.env.NODE_ENV` in your scripts. It will be set to `development` unless you use the `--production` option.

**Example:** `./app/background.js`

```javascript
if(process.env.NODE_ENV === 'development'){
  console.log('We are in development mode!');
}
```

## Testing

We use [karma](http://karma-runner.github.io/4.0/index.html), [jasmine](https://jasmine.github.io/) for unit testing and [sinon-chrome](https://github.com/acvetkov/sinon-chrome) for mocking chrome API.
Workflow: for each file that needs to be tested create a spec file next to it.
Example: for `browser-extension.js` create a `browser-extension.spec.js` file.

### Running tests

```bash
# run unit tests on your local environment using ChromeHeadless
npm run test

# run tests with `node:11` and `chromium` using docker
npm run test-docker
```

To rebuild the docker image run:

```bash
IMG=opswat/metadefender-browser-extension && VER=1.0
docker build -f Dockerfile . -t ${IMG}:${VER} -t ${IMG}:latest
```

## Contributing

See [contributing](./CONTRIBUTING.md).

### Opening Issues

If you encounter a bug we would like to hear about it. Please use [Github Issues](https://github.com/OPSWAT/metadefender-browser-extension/issues) to open new issues or search for existing ones.

## Code of conduct

See [code of conduct](./CODE_OF_CONDUCT.md).

## License

See [GPLv3](./LICENSE).

## Release notes

See [releases](https://github.com/OPSWAT/metadefender-browser-extension/releases).
