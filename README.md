# WG-Manager

This project is part of the *Effective Programming with Effects* course. I am developing a webtool to manage tasks and consumables in a shared flat. 

## Table of contents

- [Useful commands](#useful-commands)
  - [Effekt commands](#effekt-commands)
  - [Nix-related commands](#nix-related-commands)
- [Example projects](#example-projects-using-this-template)
- [Repository structure](#repository-structure)
- [CI](#ci)

---

## Useful commands

### Effekt commands

First run this:
```sh
effekt --backend=js-web src/client.effekt
```
And then this to start the server:
```sh
effekt src/server.effekt
```
You can now access the webtool on `http://localhost:3000/`

---


Open the REPL:
```sh
effekt
```

Build the project:
```sh
effekt --build src/server.effekt
```
This builds the project into the `out/` directory, creating a runnable file `out/server`.

To see all available options and backends, run:
```sh
effekt --help
```

### Nix-related commands

While Nix installation is optional, it provides several benefits:

Update dependencies (also runs automatically in CI):
```sh
nix flake update
```

Open a shell with all necessary dependencies:
```sh
nix develop
```

Run the main entry point:
```sh
nix run
```

Build the project (output in `result/bin/`):
```sh
nix build
```

## Example projects using this template

- [`effekt-stm`](https://github.com/jiribenes/effekt-stm)
- This very project!

## Repository structure

- `.github/workflows/*.yml`: Contains the [CI](#ci) definitions
- `src/`: Contains the source code
  - `main.effekt`: Main entry point
  - `test.effekt`: Entry point for tests
  - `lib.effekt`: Library code imported by `main` and `test`
- `flake.nix`: Package configuration in a Nix flake
- `flake.lock`: Auto-generated lockfile for dependencies
- `LICENSE`: Project license
- `README`: This README file

## CI

Two GitHub Actions are set up:

1. `flake-check`:
   - Checks the `flake.nix` file, builds and tests the project
   - Runs on demand, on `main`, and on PRs
   - To run custom commands, add a step using:
     - `nix run -- <ARGS>` to run the main entry point with the given arguments
     - `nix develop -c '<bash command to run>'` to run commands in the correct environment

2. `update-flake-lock`:
   - Updates package versions in `flake.nix`
   - Runs on demand and weekly (Tuesdays at 00:00 UTC)
