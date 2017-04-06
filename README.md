# api documentation for  [babel-plugin-react-transform (v2.0.2)](https://github.com/gaearon/babel-plugin-react-transform#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-babel-plugin-react-transform.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-babel-plugin-react-transform) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-babel-plugin-react-transform.svg)](https://travis-ci.org/npmdoc/node-npmdoc-babel-plugin-react-transform)
#### Babel plugin to instrument React components with custom transforms

[![NPM](https://nodei.co/npm/babel-plugin-react-transform.png?downloads=true)](https://www.npmjs.com/package/babel-plugin-react-transform)

[![apidoc](https://npmdoc.github.io/node-npmdoc-babel-plugin-react-transform/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-babel-plugin-react-transform_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-babel-plugin-react-transform/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-babel-plugin-react-transform/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-babel-plugin-react-transform/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Dan Abramov",
        "email": "dan.abramov@me.com"
    },
    "bugs": {
        "url": "https://github.com/gaearon/babel-plugin-react-transform/issues"
    },
    "dependencies": {
        "lodash": "^4.6.1"
    },
    "description": "Babel plugin to instrument React components with custom transforms",
    "devDependencies": {
        "babel-cli": "^6.2.0",
        "babel-core": "^6.2.1",
        "babel-eslint": "^4.1.6",
        "babel-preset-es2015": "^6.1.18",
        "babel-register": "^6.2.0",
        "eslint": "^1.10.3",
        "eslint-plugin-react": "^3.11.2",
        "mocha": "^2.2.5",
        "rimraf": "^2.4.3"
    },
    "directories": {},
    "dist": {
        "shasum": "515bbfa996893981142d90b1f9b1635de2995109",
        "tarball": "https://registry.npmjs.org/babel-plugin-react-transform/-/babel-plugin-react-transform-2.0.2.tgz"
    },
    "gitHead": "827843e1da56b1edbd4f9e526339d9968ada652d",
    "homepage": "https://github.com/gaearon/babel-plugin-react-transform#readme",
    "keywords": [
        "babel-plugin",
        "react-transform",
        "instrumentation",
        "dx",
        "react",
        "reactjs",
        "components"
    ],
    "license": "MIT",
    "main": "lib/index.js",
    "maintainers": [
        {
            "name": "gaearon",
            "email": "dan.abramov@gmail.com"
        },
        {
            "name": "thejameskyle",
            "email": "me@thejameskyle.com"
        }
    ],
    "name": "babel-plugin-react-transform",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/gaearon/babel-plugin-react-transform.git"
    },
    "scripts": {
        "build": "babel src -d lib",
        "clean": "rimraf lib",
        "prepublish": "npm run clean && npm run build",
        "test": "mocha --compilers js:babel-register",
        "test:watch": "npm run test -- --watch"
    },
    "version": "2.0.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module babel-plugin-react-transform](#apidoc.module.babel-plugin-react-transform)
1.  [function <span class="apidocSignatureSpan">babel-plugin-react-transform.</span>default (_ref)](#apidoc.element.babel-plugin-react-transform.default)



# <a name="apidoc.module.babel-plugin-react-transform"></a>[module babel-plugin-react-transform](#apidoc.module.babel-plugin-react-transform)

#### <a name="apidoc.element.babel-plugin-react-transform.default"></a>[function <span class="apidocSignatureSpan">babel-plugin-react-transform.</span>default (_ref)](#apidoc.element.babel-plugin-react-transform.default)
- description and source-code
```javascript
default = function (_ref) {
  var t = _ref.types;
  var template = _ref.template;

  function matchesPatterns(path, patterns) {
    return !!(0, _find2.default)(patterns, function (pattern) {
      return t.isIdentifier(path.node, { name: pattern }) || path.matchesPattern(pattern);
    });
  }

  function isReactLikeClass(node) {
    return !!(0, _find2.default)(node.body.body, function (classMember) {
      return t.isClassMethod(classMember) && t.isIdentifier(classMember.key, { name: 'render' });
    });
  }

  function isReactLikeComponentObject(node) {
    return t.isObjectExpression(node) && !!(0, _find2.default)(node.properties, function (objectMember) {
      return (t.isObjectProperty(objectMember) || t.isObjectMethod(objectMember)) && (t.isIdentifier(objectMember.key, { name: '
render' }) || t.isStringLiteral(objectMember.key, { value: 'render' }));
    });
  }

  // 'foo({ displayName: 'NAME' });' => 'NAME'
  function getDisplayName(node) {
    var property = (0, _find2.default)(node.arguments[0].properties, function (node) {
      return node.key.name === 'displayName';
    });
    return property && property.value.value;
  }

  function hasParentFunction(path) {
    return !!path.findParent(function (parentPath) {
      return parentPath.isFunction();
    });
  }

  // wrapperFunction("componentId")(node)
  function wrapComponent(node, componentId, wrapperFunctionId) {
    return t.callExpression(t.callExpression(wrapperFunctionId, [t.stringLiteral(componentId)]), [node]);
  }

  // '{ name: foo }' => Node { type: "ObjectExpression", properties: [...] }
  function toObjectExpression(object) {
    var properties = Object.keys(object).map(function (key) {
      return t.objectProperty(t.identifier(key), object[key]);
    });

    return t.objectExpression(properties);
  }

  var wrapperFunctionTemplate = template('\n    function WRAPPER_FUNCTION_ID(ID_PARAM) {\n      return function(COMPONENT_PARAM) {\n        return EXPRESSION;\n      };\n    }\n  ');

  var VISITED_KEY = 'react-transform-' + Date.now();

  var componentVisitor = {
    Class: function Class(path) {
      if (path.node[VISITED_KEY] || !matchesPatterns(path.get('superClass'), this.superClasses) || !isReactLikeClass(path.node)) {
        return;
      }

      path.node[VISITED_KEY] = true;

      var componentName = path.node.id && path.node.id.name || null;
      var componentId = componentName || path.scope.generateUid('component');
      var isInFunction = hasParentFunction(path);

      this.components.push({
        id: componentId,
        name: componentName,
        isInFunction: isInFunction
      });

      // Can't wrap ClassDeclarations
      var isStatement = t.isStatement(path.node);
      var expression = t.toExpression(path.node);

      // wrapperFunction("componentId")(node)
      var wrapped = wrapComponent(expression, componentId, this.wrapperFunctionId);
      var constId = void 0;

      if (isStatement) {
        // wrapperFunction("componentId")(class Foo ...) => const Foo = wrapperFunction("componentId")(class Foo ...)
        constId = t.identifier(componentName || componentId);
        wrapped = t.variableDeclaration('const', [t.variableDeclarator(constId, wrapped)]);
      }

      if (t.isExportDefaultDeclaration(path.parent)) {
        path.parentPath.insertBefore(wrapped);
        path.parent.declaration = constId;
      } else {
        path.replaceWith(wrapped);
      }
    },
    CallExpression: function CallExpression(path) {
      if (path.node[VISITED_KEY] || !matchesPatterns(path.get('callee'), this.factoryMethods) || !isReactLikeComponentObject(path
.node.arguments[0])) {
        return;
      }

      path.node[VISITED_KEY] = true;

      // 'foo({ displayName: 'NAME' });' => 'NAME'
      var componentName = getDisplayName(path.node);
      var componentId = componentName || path.scope.generateUid('component');
      var isInFunction = hasParentFunction(path);

      this.components.push({
        id: componentId,
        name: componentName,
        isInFunction: isInFunction
      });

      path.replaceWith(wrapC ...
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
