# Beginner’s Guide

http://nginx.org/en/linux_packages.html#Ubuntu

`nginx` has one _master_ process and several _worker_ processes.

- The main purpose of the _master_ process is to read and evaluate configuration, and maintain worker processes.
- Worker processes do actual processing of requests

The number of worker processes is defined in the configuration file may be automatically adjusted to the number of available CPU cores (see `worker_processes`)

- by default, the configuration file is named `nginx.conf`
- it's placed in `/usr/local/nginx/conf/`, `/etc/nginx/`, or `/usr/local/etc/nginx/`

## Starting, Stopping, and Reloading Configuration

To start nginx, run the executable file. Once nginx is started, it can be controlled by invoking the executable with the `-s` parameter. Use the following syntax:

`nginx -s <signal>`

Where `<signal>` may be one of the following:

- `stop` — fast shutdown
- `quit` — graceful shutdown
- `reload` — reloading the configuration file
- `reopen` — reopening the log files

### Quit

E.g to stop `nginx` processes with waiting for the worker processes to finish serving current requests, the following command can be executed:

`nginx -s quit`

This command should be executed under the same user that started nginx.

### Reload

Changes made in the configuration file will not be applied until the command to reload configuration is sent to `nginx` or it is restarted. To reload configuration, execute:

`nginx -s reload`

- Once the _master_ process receives the signal to reload configuration, it checks the syntax validity of the new configuration file and tries to apply the configuration provided in it. If this is a success, the _master_ process starts new _worker_ processes and sends messages to old _worker_ processes, requesting them to shut down
- Otherwise, the _master_ process rolls back the changes and continues to work with the old configuration.
- Old _worker_ processes, receiving a command to shut down, stop accepting new connections and continue to service current requests until all such requests are serviced. After that, the old _worker_ processes exit.

### Unix kill

A signal may also be sent to `nginx` processes with the help of Unix tools such as the `kill` utility. In this case a signal is sent directly to a process with a given process ID. The process ID of the nginx _master_ process is written, by default, to the nginx.pid in the directory `/usr/local/nginx/logs` or `/var/run`.

For example, if the _master_ process ID is 1628, to send the QUIT signal resulting in `nginx’s` graceful shutdown, execute:

`kill -s QUIT 1628`

For getting the list of all running nginx processes, the ps utility may be used, for example, in the following way:

`ps -ax | grep nginx`

See [http://nginx.org/en/docs/control.html].

## Configuration File’s Structure

`nginx` consists of modules which are controlled by directives specified in the configuration file.

Directives are divided into simple directives and block directives:

- A simple directive consists of the _name_ and _parameters_ separated by spaces and ends with a semicolon `;`.
- A block directive has the same structure as a simple directive, but instead of the semicolon it ends with a set of additional instructions surrounded by braces (`{` and `}`).
- If a block directive can have other directives inside braces, it is called a _context_ (examples: `events`, `http`, `server`, and `location`).

Directives placed in the configuration file outside of any contexts are considered to be in the main context. The `events` and `http` directives reside in the `main` context, `server` in `http`, and `location` in `server`:

## Serving Static Content

You will implement an example where, depending on the request, files will be served from different local directories: `/data/www` (which may contain HTML files) and `/data/images` (containing images).

This will require editing of the configuration file and setting up of a `server` block inside the `http` block with two `location` blocks.

The **configuration file may include several `server` blocks distinguished by ports and by server names**

Once `nginx` decides which `server` processes a request, it tests the URI specified in the request’s header against the parameters of the `location` directives defined inside the `server` block.

```nginx
http {
  server {
    location / {
      root /data/www;
    }
  }
}
```

This `location` block specifies the `/` prefix compared with the URI from the request

For matching requests, the URI will be added to the path specified in the root directive, that is, to /data/www, to form the path to the requested file on the local file system

**If there are several matching location blocks nginx selects the one with the longest prefix**

The location block above provides the shortest prefix, of length one, and so only if all other location blocks fail to provide a match, this block will be used.

Next, add the second location block.

```nginx
location /images/ {
    root /data;
}
```

It will be a match for requests starting with `/images/` (location `/` also matches such requests, but has shorter prefix).

The resulting configuration of the server block should look like this:

```nginx
server {
  location / {
    root /data/www;
  }

  location /images/ {
    root /data;
  }
}
```

This is already a working configuration of a server that listens on the standard port 80 and is accessible on the local machine at `http://localhost/`.

In response to requests with URIs starting with `/images/`, the server will send files from the `/data/images` directory. For example, in response to the `http://localhost/images/example.png` request nginx will send the `/data/images/example.png` file. If such file does not exist, nginx will send a response indicating the `404` error. Requests with URIs not starting with `/images/` will be mapped onto the `/data/www` directory. For example, in response to the `http://localhost/some/example.html` request nginx will send the `/data/www/some/example.html` file.

To apply the new configuration, start nginx if it is not yet started or send the reload signal to the nginx’s master process, by executing:

`nginx -s reload`

In case something does not work as expected, you may try to find out the reason in `access.log` and `error.log` files in the directory `/usr/local/nginx/logs` or `/var/log/nginx`.
