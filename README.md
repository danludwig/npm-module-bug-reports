# npm-module-bug-reports

## 2020-10-01 mocha devDependency adds content to node_modules after `npm install --production`

Sometime between Wednesday September 30th 2020 and Thursday October 1st 2020, the behavior of mocha during a clean `npm install --production` changed. Previously, the command resulted in no node_modules folder being created. Since, the command has consitently resulted in the creation of a node_modules folder with the following contents:

- define-properties
- es-abstract
- es-to-primitive
- function-bind
- has
- has-symbols
- is-callable
- is-date-object
- is-regex
- is-symbol
- object-inspect
- object-keys
- string.prototype.trimend
- string.prototype.trimstart

This is an important issue as AWS Lambda@Edge code packages have a size limitation of 1048576 bytes, and the automated build tooling provided by AWS SAM will include these node_modules contents, violating the service limit. The only workaround is to add a cleanup step in the build process to delete the node_modules folder after building but before publishing the package for use in Lambda@Edge.

### Steps to reproduce
1. `git clone https://github.com/danludwig/npm-module-bug-reports.git`
1. `cd npm-module-bug-reports/mocha-2020-10-bug`
1. Observe that the `./package.json` has only a devDependency on mocha
1. `npm install --production`
1. Observe that a node_modules folder was created with the above listed contents.

### Expected behavior
1. `cd ../mocha-2020-10-expected`
1. Observe that the `./package.json` has only a devDependency on chai
1. `npm install --production`
1. Observe that no node_modules folder was created.
