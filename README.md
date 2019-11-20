# nodemon-bug-repro

Reproduces a bug with nodemon 2.0.0.

## The bug

When restarting a process, such as a node.js express server which has been started using the `--exec` option nodemon geetins into an infite loop, waiting for subprocesses to finish. Reporting:

> [nodemon] still waiting for 2 subprocess(es) to finish...

Continually in the console.

Running the following command and then making a change in index.js will demonstrate the problem.

```sh
npm install && npm run debug
```

the `debug` script in `package.json` runs nodemon with the `--exec` flag used to run `npn run-script-start` which in turn starts `node index.js`. (I originally noticed this issue using `--exec` to recompile a typescript project and then start the output with node.)

Running `npm run watch` in the same repro does not exhibit the behaviour, and nor does running the same command with nodemon pinned to `v1.19.1`.

## The suspect

From the message and the versions I suspect [this PR](https://github.com/remy/nodemon/pull/1579) as having introduced the problem.
