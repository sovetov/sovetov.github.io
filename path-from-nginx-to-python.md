---
title: Path from NGINX to Python script through uWSGI or gunicorn
keywords: http server, python, wsgi, NGINX, uwsgi, gunicorn
description: Overview how request go from web server (NGINX) through uWSGI or gunicorn to Python script.
---

# HTTP Server to Python script

While there are many tutorials how to setup NGINX with uWSGI or gunicorn and Python code, almost none of them give an overview how request goes from NGINX to Python.

## Input

1. NGINX listens to TCP port.
2. Client opens connection and sends HTTP request.
3. NGINX accepts connection and parses request.

## Request within NGINX

NGINX decides, accorting to configuration, what to do with request. It can:

- Respond on its own, if it is request for static data, redirection or erroneous request. NGINX can ignore request completely closing connection without any data sent back; it's done [by specifying `return 444`](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html).
- Transform and pass request further to
  - uWSGI — [`uwsgi_pass` directive](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_pass),
  - gunicorn — [`proxy_pass` directive](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass),
  - other upstream — [`proxy_pass`](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass), [`fastcgi_pass`](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_pass), [`scgi_pass`](http://nginx.org/en/docs/http/ngx_http_scgi_module.html#scgi_pass) and [`memcached_pass`](http://nginx.org/en/docs/http/ngx_http_memcached_module.html#memcached_pass) directives.
 
## uWSGI

1. uWSGI listens to TCP port or Unix socket. (Latter is preferred because of performance.)
2. NGINX sends request formed accroding to [binary uwsgi protocol](http://uwsgi-docs.readthedocs.io/en/latest/Protocol.html). first byte of request packet header, `modifier1`, is `0`.
3. uWSGI accepts it and calls Python script.

[uwsgi protocol documentation](http://uwsgi-docs.readthedocs.io/en/latest/Protocol.html) also supports other types of requests, not only WSGI; it's defined by first byte of request packet header, `modifier1`.

[Configuring NGINX to pass requests to uWSGI](http://uwsgi-docs.readthedocs.io/en/latest/Nginx.html).

## gunicorn

1. gunicorn listens to TCP port or Unix socket. (Latter is preferred because of performance.)
2. NGINX sends (possibly modified) HTTP request.
3. gunicorn accepts it and calls Python script.

[Configuring NGINX to pass requests to gunicorn](http://docs.gunicorn.org/en/stable/deploy.html).

## Python script

CPython provides [binary API to call Python callables from native, binary code](https://docs.python.org/3.6/extending/embedding.html#pure-embedding). That's why uWSGI works specifically with CPython. Other interpreters, specifically PyPy and Jython, are supported individually.
