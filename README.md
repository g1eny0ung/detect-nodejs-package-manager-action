# detect-nodejs-package-manager-action

GitHub Action to auto-detect package manager for nodejs.

This action is mainly useful for action authors who want to automatically detect
the nodejs package manager used by the user's project. E.g. a `create-*` starter kit
that generates actions for users, in the past, you would have to prepare some templates
and then generate values based on the package manager used by the user,
and eventually output a usable action. Now you can combine this action and generate the final file directly.

## Usage

Below is a workflow that is used to test this action. You can find it in this repository.

In the `tests` directory, there are three subdirectories, which contain different lock files.
This action will be based on the lock file to detect the package manager used by the project.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        manager: [npm, yarn, pnpm]
    steps:
      - uses: actions/checkout@v4
      - name: Detect package manager
        id: package-manager
        uses: g1eny0ung/detect-nodejs-package-manager-action@main
        with:
          working-directory: ./tests/${{ matrix.manager }}
          env-name: pm
      - uses: pnpm/action-setup@v2
        if: ${{ matrix.manager == 'pnpm' }}
        name: Install pnpm
        with:
          version: 8
      - name: Install dependencies
        run: $pm install
        working-directory: ./tests/${{ matrix.manager }}
```

## Inputs

| Name                | Description                                                                                                | Required | Default |
| ------------------- | ---------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `working-directory` | The working directory to detect package manager                                                            | `false`  | `.`     |
| `env-name`          | An output env variable which contains the package manager name, see [Env name](#env-name) for more details | `false`  | `""`    |

## Env name

You need to specify an env name to get the package manager name. The default is empty, if you don't specify it, you can get the package manager name through the output value of the step:

```yaml
- name: Detect package manager
  id: package-manager
  uses: g1eny0ung/detect-nodejs-package-manager-action@main
- name: Set environment variable
  shell: bash
  run: echo "$pm=${{ steps.package-manager.outputs.pm }}" >> "$GITHUB_ENV"
- name: Install dependencies
  run: $pm install
```

## License

Under the [MIT License](./LICENSE).
