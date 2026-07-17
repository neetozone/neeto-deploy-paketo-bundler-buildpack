# Paketo Buildpack for Bundler

## `gcr.io/paketo-buildpacks/bundler`

The Bundler CNB provides the Bundler binary.

The buildpack installs Bundler onto the `$PATH` and `$GEM_PATH` which makes it available for subsequent buildpacks
and/or in the final running container.

## Integration

The Bundler CNB provides bundler as a dependency.
Downstream buildpacks can require the bundler dependency by generating a
[Build Plan TOML](https://github.com/buildpacks/spec/blob/master/buildpack.md#build-plan-toml)
file that looks like the following:

```toml
[[requires]]

  # The name of the Bundler dependency is "bundler". This value is considered
  # part of the public API for the buildpack and will not change without a plan
  # for deprecation.
  name = "bundler"

  # The Bundler buildpack supports some non-required metadata options.
  [requires.metadata]

    # Use `version` to request a specific version of `bundler`.
    # This buildpack supports specifying a semver constraint in the form of "2.*", "2.1.*",
    # or even "2.1.4".
    # Optional, defaults to the latest version of `bundler` found in the `buildpack.toml` file.
    version = "2.1.4"

    # When `build` is true, this buildpack will ensure that `bundler` is available
    # on the `$PATH` and `$GEM_PATH` for later buildpacks.
    # Optional, default false.
    build = true

    # When `launch` is true, this buildpack will ensure that `bundler` is available
    # on the `$PATH` and `$GEM_PATH` for the running application.
    # Optional, default false.
    launch = true
```
## Packaging

To package this buildpack for consumption:

```bash
./scripts/package.sh --version 0.8.48
```

This will build the buildpack for all target architectures specified in `buildpack.toml` (amd64 and arm64 by default) and create architecture-specific archives in the `build/` directory.

## Publishing

To publish this buildpack to a registry:

```bash
./scripts/publish.sh \
  --image-ref 348674388966.dkr.ecr.us-east-1.amazonaws.com/neeto-deploy/paketo/buildpack/bundler:0.8.48
```

The script will automatically:
- Read target architectures from `buildpack.toml`
- Detect architecture-specific buildpack archives
- Publish each architecture separately
- Create and push a multi-arch manifest list

## Bundler Configurations

Specifying the `Bundler` version through `buildpack.yml` configuration will be
deprecated in Bundler Buildpack v1.0.0.

To migrate from using `buildpack.yml` please set the `$BP_BUNDLER_VERSION`
environment variable at build time either directly (ex. `pack build my-app
--env BP_BUNDLER_VERSION=2.7.*`) or through a [`project.toml`
file](https://github.com/buildpacks/spec/blob/main/extensions/project-descriptor.md)

```shell
$BP_BUNDLER_VERSION="2.1.4"
```
This will replace the following structure in `buildpack.yml`:
```yaml
bundler:
  version: 2.1.4
```

## Logging Configurations

To configure the level of log output from the **buildpack itself**, set the
`$BP_LOG_LEVEL` environment variable at build time either directly (ex. `pack
build my-app --env BP_LOG_LEVEL=DEBUG`) or through a [`project.toml`
file](https://github.com/buildpacks/spec/blob/main/extensions/project-descriptor.md)
If no value is set, the default value of `INFO` will be used.

The options for this setting are:
- `INFO`: (Default) log information about the progress of the build process
- `DEBUG`: log debugging information about the progress of the build process

```shell
$BP_LOG_LEVEL="DEBUG"
```

## Compatibility

This buildpack is currently only supported on the Paketo Bionic and Jammy stack
distributions. A pre-compiled distribution of Bundler is provided for the Paketo stacks (i.e.
`io.buildpacks.stack.jammy` and `io.buildpacks.stacks.bionic`).
