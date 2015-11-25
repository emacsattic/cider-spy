# Getting CIDER SPY working with 0.10.0

This is a journal of myself fixing CIDER-SPY to.... work again basically, with the latest version of CIDER - 0.10.0. I'm a bit intimidated, so I'm writing this journal to bolster my bravery.

## nrepl-send-request

`nrepl-send-request` has gone from taking 2 args to three:

* request
* callback
* connection

Where does the callback passed into `nrepl-send-request` get called?

`nrepl-client-filter` -> `nrepl--dispatch-response` is where the callback gets executed.

There is a cider-client that wraps nrepl-client. So there is a wrapped for `nrepl-send-request`: `cider-nrepl-send-request`. It uses `(cider-current-connection)`, this is the smart nREPL connection hunting algorithm I actually originated created in CIDER. `cider-default-connection` simply returns the default (or 'current') connection. The word `current` has changed somewhat in meaning.

My code is messing around with `(cider-default-connection)`.

## Middleware

The middleware just isn't being called. Difficult to know if it's a client side problem (elisp) or server-side (middleware).

Could be we're passing the wrong buffer through to send-request, i.e. the REPL buffer as oppose to the connection buffer. Not really, seems that's the way CIDER rolls now, `cider-default-connection` just returns the REPL buffer.

Tracked the problem down, the `session` in the message is an atom, not a string. Asking on the CIDER channel.

## Remember CIDER-SPY

`cider-spy-connect-to-hub` is called when an nrepl-connection is established.