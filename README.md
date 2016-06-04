# Local Apache

This is a utility to run Apache httpd privately, best suited for these cases:

* You want to run a web server without the `root` privilege.
* You do not want to edit the system-side `httpd.conf`.
* You want to create a self-contained, disposable installation under a home or tmp directory.
* The local `httpd.conf` should be minimum as far as the server is functional.
* You want to add more configurations for experiments.

## Prerequisite

Apache 2.x must be installed in the system.

## Getting started

Run the commands as below install the directory:

    $ cd {development directory}
    $ git clone http://{the repository}
    $ cd local-apache

Deloy a web server:

    $ ./bin/local-apache {new apache directory}

Note: It is possible to install the local apache into the current directory (Git working directory), if it is for an experimental purpose.
It is not recommended if you plan to add your specific customization to the `conf` or `docs` directory.

Start the web server in the deployed directory:

    $ cd {new apache directory]
    $ ./bin/apachectl

Then you can open [http://localhost:8080/](http://localhost:8080/) with the browser.
If it doesn't work, see the error log (`logs/error.log`) and consult the Troubleshooting section below.

`./bin/apachectl` is a wrapper that works just like `apachectl`.

    $ ./bin/apachectl -k stop
    $ ./bin/apachectl -k restart
    $ ./bin/apachectl -k graceful

If you want to use a different port number, set the `SERVER_PORT` variable in `conf/local-apache.env`.

    $ {edit} conf/local-apache.env

## Directory layout

### ./bin

Utility scripts, including `apachectl` wrapper

### ./conf

Contains minimum `httpd.conf` with `Include conf/include/*.conf` directive.

You can add your additional configurations to the `include` sub-directory.

Common environment variables (including the port number) can be configured in the `local-apache.env` file.

### ./docs

This is the document root.

Any URL paths that contain `/cgi-bin/` or end with `.cgi` will invoke the scripts as CGI.

Optionally, create a symlink to use your existing document root:

    $ rm -rf docs
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

If the web server does not start up, check the output of `./bin/apachectl` or the content of `./logs/error.log` to see if there are any error messages.

In order to adjust the configuration, it is recommended to create a file named `./conf/include/local.conf` and add your own lines.

### AH00534: httpd: Configuration error: No MPM loaded.

Add an MPM module. E.g.

    LoadModule mpm_prefork_module modules/mod_mpm_prefork.so

### AH00136: Server MUST relinquish startup privileges before accepting connections.  Please ensure mod\_unixd or other system security module is loaded.

Add `mod_unixd`:

    LoadModule unixd_module modules/mod_unixd.so

### AH00025: configuration error:  couldn't check user: /

Add `mod_authz_core`:

    LoadModule authz_core_module modules/mod_authz_core.so

### No such file or directory: AH00023: Couldn't create the mpm-accept mutex

Set `Mutex` file path:

    Mutex file:run mutex-accept

Note: If you have deployed `local-apache` under an NFS or AFS directory, you need to specify a local disk file system as `file:/path/to/dir`.

## License

Licensed under the Apache License, Version 2.0 (the "License"); See the [LICENSE](LICENSE) file.
