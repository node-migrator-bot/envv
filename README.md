Envv allows you to change how your client-side code works depending on whether it's in a development or production environment. The same concept you love from server-side coding, except now you can use it in your front-end projects.

    <link rel='stylesheet' href='grid-debug.css' data-environment='development' /> 
    <script
        src='jquery.js'
        data-cdn='https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js'>
    </script>
    <script data-environment='production'>
        (function() {
        var ga = document.createElement('script');
        ga.src = ('https:' == document.location.protocol ?
            'https://ssl' : 'http://www') +
            '.google-analytics.com/ga.js';
        ga.setAttribute('async', 'async');
        document.documentElement.firstChild.appendChild(ga);
        })();
    </script>

## API

...

## CLI

...

###
data{-prefix}-environment       # development is a special value, everything else is up to you
data{-prefix}-runtime           # does the replacement if env is anything other than development
data{-prefix}-cdn               # similar to runtime, but you don't have to specify a full URL, 
                                # but instead you can do e.g. data-cdn="jquery@1.7.2"
                                # and it will check the two major public CDN's
                                # (Google and CloudFlare)
                                # (though if you pass a full URL, it'll act like a more semantic
                                # version of data-runtime instead)

## Using data-cdn

There are three different ways you can use data-cdn, to suit different requirements.

1. No specification, just <script src="/myproject/vendor/underscore/1.3.1/underscore-min.js" data-cdn></script>

Your vendor dir during development follows the common CDN directory layout, 

    <script>/<version>/<script-without-suffix>.min.js
or
    <script>/<version>/<script-without-suffix>-min.js

"Without suffix" means that `backbone.js` becomes `backbone`, and `datejs` becomes `date`. So your backbone.js file should reside at `/backbone.js/0.9.1/backbone-min.js`

Any base directory works, so for example /myproject/vendor/underscore/1.3.0/underscore-min.js is ok. Envv will simply find out if the Google or CloudFlare CDNs have the library you're looking for.

This option is especially useful when you're using [Draughtsman]() or the [Mimeo]() local mirror during development, as those tools will make popular JavaScript scripts available at exactly those paths.

2. Full specification

You can specify a full URL. This is equivalent to `data-runtime`.

    <script src="underscore.min.js" data-cdn="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.3.1/underscore-min.js"></script>

3. Semver specification

You can specify a project and its version number, and Envv will try to find that project in a public CDN.

    <script src="underscore.min.js" data-cdn="underscore@1.3.1"></script>

See [the semver website](http://semver.org/) for more information about how semantic version numbering works.

### Customizing the search path

By default, Envv will search https://ajax.googleapis.com/ajax/libs and http://cdnjs.cloudflare.com/ajax/libs. You can change where Envv looks with the `--networks` parameter on the commandline, or the networks argument to cdn.find in the API.

Whenever Envv first encounters a particular semver reference or an incompletely specified reference, it will query public CDNs to see whether they have what you're looking for. But a minority of projects use unexpected filenames and Envv can't find those. For example labjs is available as `LAB.min.js` on the 
