# Webpack

Код JavaScript часто оформляется в модули, подходящие для повторного использования. Но эти модули часто зависят от других модулей, которые могут зависеть от третьих и так далее.

Допустим, у вас есть модуль `myUtil`, который используется во многих компонентах React - **users.jsx, billing.jsx**. Вам придеться вручную следить за тем, чтобы при каждом использовании одного из этих компонентов модуль `myUtil` включался как зависимость. Кроме того, это может привести к избыточным загрузкам `myUtil` (по два, три раза). Webpack призван помочь в управлении ими.

Webpack умеет работать со всеми тремя типами [модулей](https://learn.javascript.ru/modules) JavaScript: [CommonJS](http://www.commonjs.org/), [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD), [ES6](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), так что вам не нужно беспокоиться, если вы работаете с произвольной комбинацией разнотипных модулей. Webpack проанализирует зависимости для всего кода JavaScript в вашем проекте, а затем проследит за тем, чтобы:

* Все зависимости подгружались в правильном порядке;
* Все зависимочти подгружались один раз;
* Код JS был упакован в наименьшее количество файлов (называемых _ассетами_ - static assets)

Webpack не ограничивается кодом JS. Также поддерживается предварительная обработка других статичских файлов посредством использования загрузчиков. Например, перед сборкой вы можете:

* Выполнить предварительную компиляцию файлов JSX в JS
* Выполнить предварительную компиляцию файлов Sass в CSS
* Оптимизировать двумерную графику в один файл PNP, файл JPG или встроенные ассеты

Пример **package.json** с Webpack из React Quickly ch.12:
```json
{
  "name": "webpack-example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "./node_modules/.bin/webpack -w",
    "wds-cli": "./node_modules/.bin/webpack-dev-server --inline --hot --module-bind 'css=style-loader!css-loader'  --module-bind 'jsx=react-hot-loader!babel-loader' --config webpack.dev-cli.config.js",
    "wds": "./node_modules/.bin/webpack-dev-server --config webpack.dev.config.js"
  },
  "author": "Azat Mardan",
  "license": "MIT",
  "babel": {
    "presets": [
      "react"
    ]
  },
  "dependencies": {},
  "devDependencies": {
    "babel-core": "6.13.2",
    "babel-loader": "6.4.1",
    "babel-preset-react": "6.5.0",
    "css-loader": "0.23.1",
    "react": "15.5.4",
    "react-dom": "15.5.4",
    "react-hot-loader": "1.3.1",
    "style-loader": "0.13.1",
    "webpack": "2.4.1",
    "webpack-dev-server": "2.4.2"
  }
}
```
* _Webpack_ - инструмент сборки (имя в npm: **webpack**);
* _Загрузчики_ - стили, CSS, оперативная замена модулей (HMR) и препроцессоры Babel/JSX (имена в npm: **style-loader, css-loader, react-hot-loader, babel-loader, babel-core, babel-preset-react**);
* _webpack-dev-server_ - сервер разработки Express, который позволяет использовать HMR (имя в npm: **webpack-dev-server**);

<br><br><hr>

## Литература
**React Quickly** - Azat Mardan
