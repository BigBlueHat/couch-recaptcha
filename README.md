# couch-recaptcha

Integrating [Google's reCAPTCHA](https://www.google.com/recaptcha/) on [Apache CouchDB](http://couchdb.apache.org/).

## How

reCAPTCHA is a two part invention:
 - client-side JS widget to gather the user's actions
 - server-side code to do a "background check" on those actions to "double check"

> To use reCAPTCHA, you need to <a href="http://www.google.com/recaptcha/admin">sign up for an API key pair</a> for your site. The key pair consists of a <b>site key</b> and <b>secret</b>. The <b>site key</b> is used to <a href="/recaptcha/docs/display">display the widget</a> on your site. The <b>secret</b> authorizes communication between your application backend and the reCAPTCHA server to <a href="/recaptcha/docs/verify">verify the user's response</a>. The <b>secret</b> needs to be kept safe for security purposes.
>
> -- https://developers.google.com/recaptcha/docs/start

## How on Couch?

So. CouchDB can't (on it's own) send outbound HTTP requests to other servers or APIs. However, we've got some options.

### Option 1

The `_changes` feed gives us information on every document change (including deletes) that happen in a database. We could use this on a `recaptcha-verify` database to gather the submissions from the widget, watch the `_changes` feed there, send the verification asynchronously, and then mark a related doc (perhaps in a different database) as being verified.

### Option 2

CouchDB has some extreme modularity if you dig into the config. One of the available extension points are [External Processes](http://docs.couchdb.org/en/1.6.1/config/externals.html) one type of which is known as an [Update Notification](http://docs.couchdb.org/en/1.6.1/config/externals.html#config-update-notification). These can be written in any language on the host machine--you'll note the example uses Ruby.

This approach could be coupled with the `_changes` feed to avoid polling the feed. Alternatively, this could be used with a `_view` to allow the verify docs to be mixed in with other docs and avoid a separate `recaptcha-verify` database.

### Option 3

CouchDB can also be used as [a *minimal* HTTP Proxy](http://docs.couchdb.org/en/1.6.1/config/proxying.html#couchdb-as-proxy). This could possibly be used to keep the server-side secret bit in CouchDB's config (vs. sent to the client). The `POST` request would then be sent to this proxied ("secreted") endpoint with whatever data the client knows.

### Option 4

Do it all with AJAX? It's still not 100% clear to me if a server is required. I've not found any decent flow docs to know what piece of data lives where, but the vast majority of examples point to server-side code.


## Conclusion

It's possible, but it seems it will take extension.


# License

Apache License 2.0
