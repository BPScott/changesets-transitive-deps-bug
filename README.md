### Changesets `updateInternalDependents` bug

This repository showcases a bug with changesets `updateInternalDependents` behaviour around updating `devDependencies`. It is currently updating the package version of packages that have dependencies upon packages that have devDependencies upon packages that have changed, when it should not be making these updates.

This repository has 3 packages:

- `package-a`
- `package-b` - this has a devDependency on `package-a`
- `package-c` - this has a runtime dependency on `package-b`

`package-a` is the only package with a changeset associated with it.


### Expected behaviour

When running `npm run changset version`, I would expect changsets to:

- Update `package-a`: Increase the version in the package.json and generate a CHANGELOG entry, as there is a changeset associated with this package
- Update `package-b`: Update the devDependency version of package-a to the new version i the package.json. The version of `package-b` should be untouched as only a devDependency changed. No CHANGELOG entry is created as the version does not change.
- Not touch `package-c`'s package.json. Because package-b's version was not updated there should be no change to `package-c`'s package.json or CHANGELOG.

### Actual Behaviour

The expected changes to `package-a` and `package-b`'s both occur as expected.

However modifications are made to `package-c`, when there should be none.

`package-c`'s version number is increased in the package.json. A changelog heading for the new version is generated, but no contents is present.


### Steps to reproduce

- `npm install` to install dependencies
- Run `npm run changeset version` and note the generated files. There are modifications to `package-c` when there should be none.
