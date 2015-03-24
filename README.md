# kraken-test

This is where I run experiments on krakenjs


## React-i18n Proof of Concept

### Setup

```
npm install
bower install
```

### Startup
```
grunt build
node server.js
```

### How to Internationalization React 

1 - `grunt-makara-amdify` is a build step that reads in the locales directories to generate language packs.

```js
grunt.registerTask('build', [ 'jshint', 'makara-amdify', 'less', 'require-bundle', 'i18n', 'copyto' ]);
// ~> .build/de-DE/_languagepack.js , .build/en-US/_languagepack.js
```

2 - We load the language pack into our dust template. Loading the language pack creates a named require module.

```html
<script data-main="app/app" src="/components/requirejs/require.js"></script>
<script src="/en-US/_languagepack.js"></script>
```

3 - Require in the named `_languagepack`

```js
import languagePack from '_languagepack';
/* {
 *    'index.properties': {a:'hello'},
 *    'transfer/index.properties': {transfer:'hello transfer {example}'},
 * }
```

4 - Pass in ReactIntl as a mixin

```
import ReactIntl from 'react-intl';

let Test = React.createClass({
    mixins: [ReactIntl.IntlMixin]
});

```

5 - Then pass in the language pack data as context for yahoo's react-i18n mixin.

```js
let userLang = 'en-US';

let context = {
    locales: [userLang],
    messages: languagePack[userLang]['index.properties']
};

// react-intl depends on knowing some context from _languagepack
React.render(
    <Test {...context} />,
    document.getElementById('ReactContent')
);
```

6 - Combining the language pack with react-intl is obtained using helper methods (generated from the Mixin):

```
let Test = React.createClass({

    render: function() {
        return (<div>
            <p>{    this.formatMessage(this.getIntlMessage('greeting'), {name: "World"})    }</p>
        </div>);
    }
});
```

### Glossary

- `react-intl` : yahoo's react internationalization library
- `makara-(amdifiy||browserify)` : Grunt Plugin that parses through locales directory to create spud files
- `/en-US/_languagePack.js` : The generated file
- `_languagepack` : The named-module that we require in later.
