# Local Apache

This is a utility to run Apache httpd privately, best suited for these cases:

* You want to run a web server without the `root` privilege.
* You do not want to edit the system-side `httpd.conf`.
* You want to create a self-contained, disposable installation under a home or tmp directory.
* The local `httpd.conf` should be minimum as far as the server is functional.
* You want to add more configurations for experiments.

## How it works

Confirm `httpd` is available in the `PATH`. It may reside in `/usr/sbin/httpd` which is sometimes not in the `PATH`.

    $ which httpd

Run the commands as below:

    $ cd {your directory}
    $ git clone {the repository}
    $ cd local-apache
    $ ./bin/apachectl

Create an `index.html` file:

    $ echo Hello World > docs/index.html

Then you can open [http://localhost:8080/](http://localhost:8080/) with the browser.
If it doesn't work, see the error log (`logs/error.log`) and consult the Troubleshooting section below.

`./bin/apachectl` is a wrapper that works just like `apachectl`.

    $ ./bin/apachectl -k stop
    $ ./bin/apachectl -k restart
    $ ./bin/apachectl -k graceful

If you want to use a different port number, set the `SERVER_PORT` environment variable.

    $ SERVER_PORT=5555 ./bin/apachectl

## Directory layout

### ./bin

Utility scripts, including `apachectl` wrapper

### ./conf

Contains minimum `httpd.conf` with `Include conf/include/*.conf` directive.

You can add your additional configurations to the `include` sub-directory.

Any files with the `bundled-` prefix indicate pre-bundled in this distribution (git repository).
In addition, `conf/include/local.conf` is automatically generated (only when it does not exist) to include environment-specific configuration.

### ./docs

This is the document root.

Any URL paths that contain `/cgi-bin/` or end with `.cgi` will invoke the scripts as CGI.

Optionally, create a symlink to use your existing document root:

    $ rmdir docs
    $ ln -s /existing/document/root docs

### ./logs

* access.log
* error.log
* cgi.log

### ./modules

Unless the directory exists at startup time, a symlink is automatically created.

### ./run

* httpd.pid
* accept.lock.*

## Troubleshooting

### AH00534: httpd: Configuration error: No MPM loaded.

Add an MPM module. E.g.

    LoadModule mpm_prefork_module modules/mod_mpm_prefork.so

### AH00136: Server MUST relinquish startup privileges before accepting connections.  Please ensure mod_unixd or other system security module is loaded.

Add `mod_unixd`:

    LoadModule unixd_module modules/mod_unixd.so

### AH00025: configuration error:  couldn't check user: /

Add `mod_authz_core`:

    LoadModule authz_core_module modules/mod_authz_core.so

## License

Licensed under the Apache License, Version 2.0 (the "License"); See the [LICENSE](LICENSE) file.
