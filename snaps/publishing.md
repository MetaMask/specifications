# Snaps Publishing Specification v0.1

Snaps are SES-compatible JavaScript programs that integrate with [MetaMask] or an equivalent "host application" across a process isolation and serialization boundary.
They communicate with the outside world via asynchronous [JSON-RPC 2.0] message passing.

In the future, Snaps may be published to many different locations.
At first, they will only be published to [npm].

## Specification Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC-2119](https://www.ietf.org/rfc/rfc2119.txt).

> Comments like this are non-normative.

## Published Snap File Structure

The published files of an example Snap published to npm under the package name `@metamask/example-snap` may look like this:

```text
example-snap/
├─ dist/
│  ├─ bundle.js
├─ package.json
├─ README.md
├─ snap.manifest.json
```

### `dist/bundle.js`

The Snap "source" or "bundle" file can be named anything and kept anywhere in the file hierarchy, but it **MUST**:

* contain the entire source of the Snap program, including all dependencies.
* execute under [SES].
* be compatible with the [MetaMask Snaps API](#dist-bundle-js). <!-- TODO: Add link -->

### `package.json`

The `package.json` file **MUST** adhere to [the requirements of npm](https://docs.npmjs.com/cli/v7/configuring-npm/package-json).

Its `version` field **MUST** correspond to [that of `snap.manifest.json`](#version).

### `README.md`

Just like any npm package, a Snap **SHOULD** contain a helpful readme file.

### `snap.manifest.json`

See [the Snap manifest specification](#snap-manifest-files) below.

## Snap Manifest Files

The Snap manifest file **MUST** be named `snap.manifest.json` and located in the package root directory.

Continuing with the example of the Snap published to npm as `@metamask/example-snap`, its manifest could look something like this:

```json
    {
      "version": "0.2.2",
      "proposedName": "@metamask/example-snap",
      "description": "An example snap.",
      "repository": {
        "type": "git",
        "url": "https://github.com/MetaMask/example-snap.git"
      },
      "source": {
        "shasum": "w3FltkDjKQZiPwM+AThnmypt0OFF7hj4ycg/kxxv+nU=",
        "location": {
          "npm": {
            "filePath": "dist/bundle.js",
            "packageName": "@metamask/example-snap",
            "registry": "https://registry.npmjs.org/"
          }
        }
      },
      "initialPermissions": {
        "snap_confirm": {}
      },
      "manifestVersion": "0.1"
    }
```

### `version`

**MUST** be a valid [SemVer] version string and equal to the [corresponding `package.json` field](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#version).

### `proposedName`

**MUST** be a string less than or equal to 214 characters. <!-- This is what npm uses for the `name` field -->
The Snap author's proposed name for the Snap.
The Snap host application may display this name unmodified in its user interface.

The proposed name **SHOULD** be human-readable.

### `description`

**MUST** be a non-empty string.
A short description of the Snap.
The Snap host application may display this name unmodified in its user interface.
**MAY** differ from the [corresponding `package.json` field](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#description-1)

### `repository`

**MAY** be omitted.
If present, **MUST** be equal to the [corresponding `package.json` field](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#repository).

### `source`

**MUST** be an object property with the fields specified in this subsection.

#### `shasum`

**MUST** be the [Base64-encoded] string representation of the [SHA-256] hash of the [Snap source file](#dist-bundle-js).

#### `location`

**MUST** be an object containing the [`npm` field](#npm) as specified in the [Location-Specific Manifest Fields](#hosting-platform-manifest-fields) section.

### `initialPermissions`

**MUST** be a valid [EIP-2255] `wallet_requestPermissions` parameter object, specifying the initial permissions that will be requested when the Snap is added to the host application.

### `manifestVersion`

**MUST** be the string `0.1`.

## Hosting Platform Manifest Fields

This section specifies the manifest fields nested under the [`location`](#location) field, which contain the necessary information to fetch the Snap source file.
This section may be expanded to include other hosting platforms in the future, but for now only specifies [`npm`](#npm).

### `npm`

**MUST** be an object property with the fields specified in this subsection.

#### `filePath`

**MUST** be the [Unix-style](https://en.wikipedia.org/wiki/Unix_filesystem) path from the package root directory to the Snap source file.
**MUST** begin with the name of the Snap source file or the first directory in the path to the Snap source file.
**MUST NOT** begin with the string "`./`".

#### `packageName`

**MUST** be equal to the [`name` field of `package.json`](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#name).

#### `registry`

**MUST** be the HTTPS URL of the npm registry where the package is hosted.
**MUST** be equal to the registry URL specified in the [`publishConfig` field of `package.json`](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#publishconfig), if any.

[Base64]: https://developer.mozilla.org/en-US/docs/Glossary/Base64
[EIP-2255]: https://eips.ethereum.org/EIPS/eip-2255
[JSON-RPC 2.0]: https://www.jsonrpc.org/specification
[MetaMask]: https://metamask.io
[npm]: https://npmjs.com
[SHA-256]: https://en.wikipedia.org/wiki/SHA-2
[SemVer]: https://semver.org/
[SES]: https://agoric.com/documentation/guides/js-programming/ses/ses-guide.html
