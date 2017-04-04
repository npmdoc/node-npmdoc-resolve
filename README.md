# api documentation for  [resolve (v1.3.2)](https://github.com/substack/node-resolve#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-resolve.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-resolve) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-resolve.svg)](https://travis-ci.org/npmdoc/node-npmdoc-resolve)
#### resolve like require.resolve() on behalf of files asynchronously and synchronously

[![NPM](https://nodei.co/npm/resolve.png?downloads=true)](https://www.npmjs.com/package/resolve)

[![apidoc](https://npmdoc.github.io/node-npmdoc-resolve/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-resolve_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-resolve/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-resolve/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-resolve/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "James Halliday",
        "email": "mail@substack.net",
        "url": "http://substack.net"
    },
    "bugs": {
        "url": "https://github.com/substack/node-resolve/issues"
    },
    "dependencies": {
        "path-parse": "^1.0.5"
    },
    "description": "resolve like require.resolve() on behalf of files asynchronously and synchronously",
    "devDependencies": {
        "object-keys": "^1.0.11",
        "safe-publish-latest": "^1.1.1",
        "tap": "0.4.13",
        "tape": "^4.6.3"
    },
    "directories": {},
    "dist": {
        "shasum": "1f0442c9e0cbb8136e87b9305f932f46c7f28235",
        "tarball": "https://registry.npmjs.org/resolve/-/resolve-1.3.2.tgz"
    },
    "gitHead": "781da847169b8ba43f65ed3d9dbc1283d5bde74c",
    "homepage": "https://github.com/substack/node-resolve#readme",
    "keywords": [
        "resolve",
        "require",
        "node",
        "module"
    ],
    "license": "MIT",
    "main": "index.js",
    "maintainers": [
        {
            "name": "ljharb",
            "email": "ljharb@gmail.com"
        },
        {
            "name": "substack",
            "email": "substack@gmail.com"
        }
    ],
    "name": "resolve",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/substack/node-resolve.git"
    },
    "scripts": {
        "prepublish": "safe-publish-latest",
        "test": "npm run --silent tests-only",
        "tests-only": "tape test/*.js"
    },
    "version": "1.3.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module resolve](#apidoc.module.resolve)
1.  [function <span class="apidocSignatureSpan">resolve.</span>isCore (x)](#apidoc.element.resolve.isCore)
1.  [function <span class="apidocSignatureSpan">resolve.</span>sync (x, options)](#apidoc.element.resolve.sync)
1.  object <span class="apidocSignatureSpan">resolve.</span>core



# <a name="apidoc.module.resolve"></a>[module resolve](#apidoc.module.resolve)

#### <a name="apidoc.element.resolve.isCore"></a>[function <span class="apidocSignatureSpan">resolve.</span>isCore (x)](#apidoc.element.resolve.isCore)
- description and source-code
```javascript
function isCore(x) { return core[x]; }
```
- example usage
```shell
...
        try { return fs.statSync(file).isFile() }
        catch (e) { return false }
    },
    moduleDirectory: 'node_modules'
}
''''

## resolve.isCore(pkg)

Return whether a package is in core.

# install

With [npm](https://npmjs.org) do:
...
```

#### <a name="apidoc.element.resolve.sync"></a>[function <span class="apidocSignatureSpan">resolve.</span>sync (x, options)](#apidoc.element.resolve.sync)
- description and source-code
```javascript
sync = function (x, options) {
    var opts = options || {};
    var isFile = opts.isFile || function (file) {
        try {
            var stat = fs.statSync(file);
        } catch (e) {
            if (e && e.code === 'ENOENT') return false;
            throw e;
        }
        return stat.isFile() || stat.isFIFO();
    };
    var readFileSync = opts.readFileSync || fs.readFileSync;

    var extensions = opts.extensions || ['.js'];
    var y = opts.basedir || path.dirname(caller());

    opts.paths = opts.paths || [];

    if (/^(?:\.\.?(?:\/|$)|\/|([A-Za-z]:)?[\\\/])/.test(x)) {
        var res = path.resolve(y, x);
        if (x === '..') res += '/';
        var m = loadAsFileSync(res) || loadAsDirectorySync(res);
        if (m) return m;
    } else {
        var n = loadNodeModulesSync(x, y);
        if (n) return n;
    }

    if (core[x]) return x;

    var err = new Error("Cannot find module '" + x + "' from '" + y + "'");
    err.code = 'MODULE_NOT_FOUND';
    throw err;

    function loadAsFileSync(x) {
        if (isFile(x)) {
            return x;
        }

        for (var i = 0; i < extensions.length; i++) {
            var file = x + extensions[i];
            if (isFile(file)) {
                return file;
            }
        }
    }

    function loadAsDirectorySync(x) {
        var pkgfile = path.join(x, '/package.json');
        if (isFile(pkgfile)) {
            var body = readFileSync(pkgfile, 'utf8');
            try {
                var pkg = JSON.parse(body);
                if (opts.packageFilter) {
                    pkg = opts.packageFilter(pkg, x);
                }

                if (pkg.main) {
                    var m = loadAsFileSync(path.resolve(x, pkg.main));
                    if (m) return m;
                    var n = loadAsDirectorySync(path.resolve(x, pkg.main));
                    if (n) return n;
                }
            } catch (e) {}
        }

        return loadAsFileSync(path.join(x, '/index'));
    }

    function loadNodeModulesSync(x, start) {
        var dirs = nodeModulesPaths(start, opts);
        for (var i = 0; i < dirs.length; i++) {
            var dir = dirs[i];
            var m = loadAsFileSync(path.join(dir, '/', x));
            if (m) return m;
            var n = loadAsDirectorySync(path.join(dir, '/', x));
            if (n) return n;
        }
    }
}
```
- example usage
```shell
...
/home/substack/projects/node-resolve/node_modules/tap/lib/main.js
'''

synchronously resolve:

''' js
var resolve = require('resolve');
var res = resolve.sync('tap', { basedir: __dirname });
console.log(res);
'''

'''
$ node example/sync.js
/home/substack/projects/node-resolve/node_modules/tap/lib/main.js
'''
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
