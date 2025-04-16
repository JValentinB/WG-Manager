# WG-Manager

This project is part of the *Effective Programming with Effects* course. I developed a webtool to manage tasks and consumables in a shared flat. 

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

