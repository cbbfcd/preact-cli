# preact-cli [![NPM Downloads](https://img.shields.io/npm/dm/preact-cli.svg)](https://www.npmjs.com/package/preact-cli) [![NPM Version](https://img.shields.io/npm/v/preact-cli.svg)](https://www.npmjs.com/package/preact-cli)

> Start building a [Preact] Progressive Web App in seconds 🔥

> **Note**
> `preact-cli` no longer sees active development! It's stable so you can rely upon it for all of your existing apps, but for creating new ones, we recommend Vite via [`create-preact`](https://github.com/preactjs/create-preact). It's a much faster, more modern experience and we're working to bring some Preact-CLI/WMR features to it.

### Contents

- [Features](#features)
- [Usage](#usage)
- [Official Templates](#official-templates)
- [CLI Options](#cli-options)
  - [preact create](#preact-create)
  - [preact build](#preact-build)
  - [preact watch](#preact-watch)
  - [preact list](#preact-list)
  - [preact info](#preact-info)
- [Pre-rendering](#pre-rendering)
- [Custom Configuration](#custom-configuration)
  - [Plugins](#plugins)
  - [Browserslist](#browserslist)
  - [Babel](#babel)
  - [Webpack](#webpack)
  - [Prerender multiple routes](#prerender-multiple-routes)
  - [Template](#template)
- [Using CSS preprocessors](#using-css-preprocessors)
  - [SASS](#sass)
  - [LESS](#less)
- [Using Environment Variables](#using-environment-variables)
- [Route-Based Code Splitting](#route-based-code-splitting)

### Features

- **100/100 Lighthouse score**, right out of the box ([proof])
- Fully **automatic code splitting** for routes _(see [Route-Based Code Splitting](#route-based-code-splitting))_
- Transparently code-split any component with an [`async!`] prefix
- Auto-generated [Service Workers] for offline caching powered by [Workbox]
- [PRPL] pattern support for efficient loading
- Zero-configuration pre-rendering / server-side rendering hydration
- Support for CSS Modules, LESS, Sass, Stylus; with Autoprefixer
- Monitor your bundle/chunk sizes with built-in tracking
- Automatic app mounting, debug helpers & Hot Module Replacement
- In just **4.5kb** you get a productive environment:
  - [preact]
  - [preact-router]
  - 1.5kb of conditionally-loaded polyfills for [fetch] & [Promise]

### Requirements

> **Important**: [Node.js](https://nodejs.org/en/) >= v14.14 is required.

### Usage

```sh
$ npm init preact-cli <template-name> <project-name>

$ yarn create preact-cli <template-name> <project-name>
```

Example:

```sh
$ npm init preact-cli default my-project
```

The above command pulls the template from [preactjs-templates/default] and generates the project at `./my-project/`.

### Official Templates

The purpose of official preact project templates are to provide opinionated development tooling setups so that users can get started with actual app code as fast as possible. However, these templates are un-opinionated in terms of how you structure your app code and what libraries you use in addition to preact.js.

All official project templates are repos in the [preactjs-templates organization]. When a new template is added to the organization, you will be able to run `npm init preact-cli <template-name> <project-name>` to use that template.

Current available templates include:

- [default] - Default template with all features

- [simple] - The simplest possible preact setup in a single file

- [netlify] - Netlify CMS template using preact

- [typescript] - Default template implemented in TypeScript

- [widget] - Template for a widget to be embedded in another website

- [widget-typescript] - Widget template implemented in TypeScript

> 💁 Tip: Any Github repo with a `'template'` folder can be used as a custom template: <br /> `npm init preact-cli <username>/<repository> <project-name>`

### CLI Options

#### preact list

Lists all the official preactjs-cli repositories

```sh
$ [npm init / yarn create] preact-cli list
```

#### preact create

Create a project to quick start development.

```
$ [npm init / yarn create] preact-cli <template-name> <project-name>

  --name        The application name.
  --cwd         A directory to use instead of $PWD.
  --force       Force option to create the directory for the new app  [boolean] [default: false]
  --git         Initialize version control using git.                 [boolean] [default: false]
  --install     Installs dependencies.                                [boolean] [default: true]
```

#### preact build

Create a production build

You can disable `default: true` flags by prefixing them with `--no-<option>`; for example, `--no-sw` and `--no-prerender`.

```
$ [npm run / yarn] preact build

    --src              Specify source directory  (default src)
    --dest             Specify output directory  (default build)
    --cwd              A directory to use instead of $PWD  (default .)
    --sw               Generate and attach a Service Worker  (default true)
    --babelConfig      Path to custom Babel config (default .babelrc)
    --prerender        Renders route(s) into generated static HTML  (default true)
    --prerenderUrls    Path to pre-rendered routes config  (default prerender-urls.json)
    --template         Path to custom EJS or HTML template  (default 'src/template.ejs')
    --analyze          Launch interactive Analyzer to inspect production bundle(s) (default false)
    -c, --config       Path to custom CLI config  (default preact.config.js)
    -v, --verbose      Verbose output
    -h, --help         Displays this message
```

#### preact watch

Spin up a development server with multiple features like `hot-module-replacement`, `module-watcher`

```
$ [npm run / yarn] preact watch

    --src              Specify source directory  (default src)
    --cwd              A directory to use instead of $PWD  (default .)
    --clear            Clear the console (default true)
    --sw               Generate and attach a Service Worker  (default false)
    --babelConfig      Path to custom Babel config (default .babelrc)
    --https            Run server with HTTPS protocol
    --key              Path to PEM key for custom SSL certificate
    --cert             Path to custom SSL certificate
    --cacert           Path to optional CA certificate override
    --prerender        Pre-render static content on first run
    --prerenderUrls    Path to pre-rendered routes config  (default prerender-urls.json)
    --template         Path to custom EJS or HTML template  (default 'src/template.ejs')
    --refresh          Enables experimental preact-refresh functionality
    -c, --config       Path to custom CLI config  (default preact.config.js)
    -H, --host         Set server hostname  (default 0.0.0.0)
    -p, --port         Set server port  (default 8080)
    -h, --help         Displays this message
```

Note:

1. You can run dev server using `HTTPS` then you can use the following `HTTPS=true preact watch`
2. You can run the dev server on a different port using `PORT=8091 preact watch`

#### preact info

Prints debugging information concerning the local environment.

```sh
$ [npm run / yarn] preact info
```

### Pre-rendering

Preact CLI in order to follow [PRPL] pattern renders initial route (`/`) into generated static `index.html` - this ensures that users get to see your page before any JavaScript is run, and thus providing users with slow devices or poor connection your website's content much faster.

Preact CLI does this by rendering your app inside node - this means that we don't have access to DOM or other global variables available in browsers, similar how it would be in server-side rendering scenarios. In case you need to rely on browser APIs you could:

- drop out of prerendering by passing `--no-prerender` flag to `preact build`,
- write your code in a way that supports server-side rendering by wrapping code that requires browser's APIs in conditional statements `if (typeof window !== "undefined") { ... }` ensuring that on server those lines of code are never reached. Alternatively you could use a helper library like [window-or-global](https://www.npmjs.com/package/window-or-global).

### Custom Configuration

#### Plugins

To make customizing your configuration easier, preact-cli supports plugins. Visit the [Plugins wiki] for a tutorial on how to use them.

#### Browserslist

You may customize your list of supported browser versions by declaring a [`"browserslist"`] key within your `package.json`. Changing these values will modify your legacy JavaScript (via [`@babel/preset-env`]) and your CSS (via [`autoprefixer`](https://github.com/postcss/autoprefixer)) output.

By default, `preact-cli` emulates the following config:

> `package.json`

```json
{
	"browserslist": ["> 0.5%", "last 2 versions", "Firefox ESR", "not dead"]
}
```

#### Babel

To customize Babel, you have two options:

1. You may create a [`.babelrc`] file in your project's root directory, or use the `--babelConfig` path to point at any valid [Babel config file]. Any settings you define here will be merged into the [Preact CLI preset]. For example, if you pass a `"plugins"` object containing different plugins from those already in use, they will simply be added on top of the existing config. If there are conflicts, you'll want to look into option 2, as the default will take precedence.

2. If you'd like to modify the existing Babel config you must use a `preact.config.js` file. Visit the [Webpack](#webpack) section for more info, or check out the [Customize Babel] example!

#### Webpack

To customize Preact-CLI's Webpack config, create a `preact.config.js` or a `preact.config.json` file:

> `preact.config.js`

```js
// ... imports or other code up here ...

/**
 * Function that mutates the original webpack config.
 * Supports asynchronous changes when a promise is returned (or it's an async function).
 *
 * @param {import('preact-cli').Config} config - original webpack config
 * @param {import('preact-cli').Env} env - current environment and options pass to the CLI
 * @param {import('preact-cli').Helpers} helpers - object with useful helpers for working with the webpack config
 * @param {Record<string, unknown>} options - this is mainly relevant for plugins (will always be empty in the config), default to an empty object
 */
export default (config, env, helpers, options) => {
	/** you can change the config here **/
};
```

See [Webpack config helpers wiki] for more info on the `helpers` argument which contains methods to find various parts of configuration. Additionally see our [recipes wiki] containing examples on how to change webpack configuration.

#### Prerender multiple routes

The `--prerender` flag will prerender by default only the root of your application.
If you want to prerender other routes you can create a `prerender-urls.json` file, which contains the set of routes you want to render.
The format required for defining your routes is an array of objects with a `url` key and an optional `title` key.

> `prerender-urls.json`

```json
[
	{
		"url": "/",
		"title": "Homepage"
	},
	{
		"url": "/route/random"
	}
]
```

You can customise the path and/or name of `prerender-urls.json` by using the flag `--prerenderUrls`.

```sh
preact build --prerenderUrls src/prerender-urls.json
```

If a static JSON file is too restrictive, you may want to provide a javascript file that exports your routes instead.
Routes can be exported as a JSON string or an object and can optionally be returned from a function.

> `prerender-urls.js`

```js
module.exports = [
	{
		url: '/',
		title: 'Homepage',
	},
	{
		url: '/route/random',
	},
];
```

> `prerender-urls.js`

```js
export default () => {
	return [
		{
			url: '/',
			title: 'Homepage',
		},
		{
			url: '/route/random',
		},
	];
};
```

#### Template

To customize the HTML document that your app uses, edit the `template.ejs` file in your app's source directory.

[EJS](https://ejs.dev) is a simple templating language that lets you generate HTML markup with plain JavaScript. Alongside `html-webpack-plugin`, you're able to conditionally add HTML, access your bundles and assets, and link to external content if you wish. The default we provide on project initialization should fit the majority of use cases very well, but feel free to customize!

You can customize the location of your template with the `--template` flag on the `build` and `watch` commands:

```sh
preact build --template renamed-src/template.ejs
preact watch --template template.ejs
```

### Using CSS preprocessors

The default templates comes with a `.css` file for each component. You can start using CSS preprocessors at any given time during your project lifecycle by installing additional packages and then simply replacing those `.css` files.

#### [SASS]

- `npm install --save-dev sass sass-loader@10` (inside your preact application folder)
- start replacing `.css` files with `.scss` files

#### [LESS]

- `npm install --save-dev less less-loader@7` (inside your preact application folder)
- start replacing `.css` files with `.less` files

### Using Environment Variables

You can reference and use any environment variable in your application that has been prefixed with `PREACT_APP_` automatically:

> `src/index.js`

```js
console.log(process.env.PREACT_APP_MY_VARIABLE);
```

If your variable is not prefixed, you can still add it manually by using your `preact.config.js` (see [DefinePlugin] config in the recipes wiki).

It's important to note that `DefinePlugin` does direct text replacement; it does not make `process` usable. `process.env` could be an empty object, yet `process.env.PREACT_APP_MY_VARIABLE` will still get automatically replaced (if a value exists).

You can set and store variables using a `.env` file in the root of your project:

> `.env`

```
PREACT_APP_MY_VARIABLE="my-value"
```

You can also reference environment variables in your `preact.config.js`:

```js
export default (config, env, helpers, options) => {
	if (process.env.MY_VARIABLE) {
		/** You can add a config here that will only used when your variable is truthy **/
	}
};
```

### Route-Based Code Splitting

"Route" components are automatically code-splitted at build time to create smaller bundles and avoid loading more code than is needed by each page. This works by intercepting imports for route components with an [async loader](https://github.com/preactjs/preact-cli/tree/master/packages/async-loader), which returns a lightweight wrapper component that handles code splitting seamlessly.

Automatic code splitting is applied to all JavaScript and TypeScript files in the following locations:

<table>
<thead><tr><th>Pattern</th><th>Examples</th></tr></thead>
<tbody>
<tr><td>
<pre>src/<b>routes</b>/<kbd>NAME</kbd></pre>
</td><td>
<code>src/routes/home.js</code><br>
<code>src/routes/about/index.tsx</code>
</td></tr>
<tr><td>
<pre>src/components/<b>routes</b>/<kbd>NAME</kbd></pre>
</td><td>
<code>src/components/routes/profile.ts</code><br>
<code>src/components/routes/profile/index.js</code>
</td></tr>
<tr><td>
<pre>src/components/<b>async</b>/<kbd>NAME</kbd></pre>
</td><td>
<code>src/components/async/profile.ts</code><br>
<code>src/components/async/profile/index.js</code>
</td></tr>
</tbody></table>

> **Note**:
> Automatic code splitting **only** supports default exports, not named exports:
>
> ```diff
> - import { Profile } from './routes/profile';
> + import Profile from './routes/profile';
> ```
>
> This is an intentional limitation that ensures effective code splitting. For components that need named exports, place them in a directory that doesn't trigger automatic code splitting. You can then manually code-split the default export by re-exporting it from `routes/` or importing it with the `"async!"` prefix.

[promise]: https://npm.im/promise-polyfill
[fetch]: https://github.com/developit/unfetch
[preact]: https://github.com/preactjs/preact
[webpackconfighelpers]: docs/webpack-helpers.md
[`.babelrc`]: https://babeljs.io/docs/usage/babelrc
[babel config file]: https://babeljs.io/docs/en/config-files
[simple]: https://github.com/preactjs-templates/simple
[`"browserslist"`]: https://github.com/ai/browserslist
[default]: https://github.com/preactjs-templates/default
[workbox]: https://developers.google.com/web/tools/workbox
[preact-router]: https://github.com/preactjs/preact-router
[netlify]: https://github.com/preactjs-templates/netlify
[typescript]: https://github.com/preactjs-templates/typescript
[widget]: https://github.com/preactjs-templates/widget
[widget-typescript]: https://github.com/preactjs-templates/widget-typescript
[plugins wiki]: https://github.com/preactjs/preact-cli/wiki/Plugins
[preactjs-templates organization]: https://github.com/preactjs-templates
[preactjs-templates/default]: https://github.com/preactjs-templates/default
[webpack config helpers wiki]: https://github.com/preactjs/preact-cli/wiki/Webpack-Config-Helpers
[recipes wiki]: https://github.com/preactjs/preact-cli/wiki/Config-Recipes
[prpl]: https://developers.google.com/web/fundamentals/performance/prpl-pattern
[`@babel/preset-env`]: https://babeljs.io/docs/en/babel-preset-env.html
[proof]: https://googlechrome.github.io/lighthouse/viewer/?gist=142af6838482417af741d966e7804346
[preact cli preset]: https://github.com/preactjs/preact-cli/blob/master/packages/cli/src/lib/babel-config.js
[service workers]: https://developers.google.com/web/fundamentals/getting-started/primers/service-workers
[customize babel]: https://github.com/preactjs/preact-cli/wiki/Config-Recipes#customising-babel-options-using-loader-helpers
[`async!`]: https://github.com/preactjs/preact-cli/blob/1.4.1/examples/full/src/components/app.js#L7
[sass]: https://sass-lang.com
[less]: http://lesscss.org
[defineplugin]: https://github.com/preactjs/preact-cli/wiki/Config-Recipes#use-environment-variables-in-your-application
