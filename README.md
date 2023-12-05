# Try Runtime Github Actions (docker based)

This action uses [`try-runtime-cli`](http://github.com/paritytech/try-runtime-cli) to test your runtime migrations.
It will expect to have a snapshot passed as an argument and otherwise will take it for you if it doesn't exists yet.

## Inputs

### `url`

**Required** URL of the node to take a snapshot from.

### `snap`

Path to a previsouly taken snapshot, if doesn't exists we will create it.

### `runtime`

**Required** Path to a try-runtime enabled runtime to test migrations for.

> You runtime must be compiled with `try-runtime` turned on.

### `checks`

Checks to run, default to `all`.

## Example Usage

```yaml
uses: NodleCode/action-try-runtime
with:
  url: 'https://rpc.nodleprotocol.io'
  runtime: 'path/to/runtime.wasm'
```

## Wishlist

- [ ] set outputs to something useful if possible

## Development

### Upgrading to a another version of `try-runtime`

> By convention please follow the release numbers from [Parity's repo](https://github.com/paritytech/try-runtime-cli).

1. Run the [prebuild github action](https://github.com/NodleCode/action-try-runtime/actions/workflows/base-image.yml) to pre-build `try-runtime`. Pass it the latest version from parity's repo (for example `v0.5.1`).
2. Wait for the action to build succesfully, which will take a little while.
3. Update the first line in the action [`Dockerfile`](./Dockerfile) to use the version number of the new prebuilt container (in this example `v0.5.1`).
4. Draft a new github release for the same version number (`v0.5.1` in this example).
5. You may now use the newly release and built action in your CI/CD pipeline.

> We prebuild a docker container with `try-runtime` to ensure Github does not rebuilt `try-runtime` everytime we use this action, thus giving us a nice speed improvement.

### Troubleshooting

Verify online version

```
docker run  ghcr.io/nodlecode/try-runtime-cli:v0.5.1 try-runtime  -V
```

Execute the prebuilt docker image

```
docker run -v ~/nodle/chain/snapshots:/snapshots ghcr.io/nodlecode/try-runtime-cli:v0.5.1 try-runtime --runtime ./snapshots/runtime_eden.wasm   on-runtime-upgrade  snap -p snapshots/eden-ci
```

Build and run image locally
```
cd base
docker build --build-arg VERSION=v0.5.1  . -t milkman -f ./Dockerfile

docker run milkman try-runtime --version
docker run milkman try-runtime --help
docker run -v ~/nodle/chain/snapshots:/snapshots milkman try-runtime --runtime ./snapshots/runtime_eden.wasm   on-runtime-upgrade  snap -p snapshots/eden-ci
```
