# nodemon-bug-repro

Reproduces a bug with nodemon 2.0.0.

https://github.com/remy/nodemon/issues/1633

## The bug

When restarting a process, such as a node.js express server which has been started using the `--exec` option nodemon gets into an infite loop, waiting for subprocesses to finish. Reporting

> [nodemon] still waiting for 2 subprocess(es) to finish...

continually in the console, interleaved with the normal messaging for restarting the process.

Running the following command and then making a change in index.js will demonstrate the problem.

```sh
npm install && npm run debug
```

The `debug` script in `package.json` runs nodemon with the `--exec` flag used to run `npn run-script-start` which in turn starts `node index.js`. (I originally noticed this issue using `--exec` to recompile a typescript project and then start the output with node.)

Running `npm run watch` in the same repro does not exhibit the behaviour, and nor does running the same command with nodemon pinned to `v1.19.1`.

## The suspect

From the message and the versions I suspect [this PR](https://github.com/remy/nodemon/pull/1579) as having introduced the problem.

## More Logs

```
 $ npm run debug

> nodemon-bug-repro@1.0.0 debug /home/josh/src/nodemon-bug-repro
> nodemon --exec 'npm run-script start'

[nodemon] 2.0.0
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `npm run-script start`

> nodemon-bug-repro@1.0.0 start /home/josh/src/nodemon-bug-repro
> node ./index.js

Listening on port 4000!
[nodemon] restarting due to changes...
User defined signal 2
[nodemon] starting `npm run-script start`
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...

> nodemon-bug-repro@1.0.0 start /home/josh/src/nodemon-bug-repro
> node ./index.js

[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
Listening on port 4000!
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
[nodemon] still waiting for 2 subprocess(es) to finish...
```
