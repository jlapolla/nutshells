# Puppet in a nutshell

Puppet is a configuration management tool.

## Configuration management

What is configuration management?

### Manual configuration

Imagine you are setting up a web server to host a company blog. IT
provides you with a virtual machine, and a username and password to log
into that virtual machine. You log into the virtual machine, and see
what is installed on it.

Well, first you need to install Apache HTTP Server. You go to the Apache
website and follow their instructions to install Apache HTTP Server 2.4.
Then you make a file `/etc/apache2/httpd.conf` to configure Apache to
serve your company blog. Finally, you start the `httpd` service, and
configure it to listen on port `0.0.0.0:80` for the HTTP protocol, and
listen on `0.0.0.0:443` for the HTTPS protocol.

Since you're thinking ahead about future changes you may need to make on
the server, you create a new `admin` user with password `abc123`, so you
can log in as `admin` and manage the server.

### Expressing a configuration

Looking back at what we did to manually configure our server, we might
express our server's configuration as follows:

```
# Blog server configuration
# Note: This is not valid Puppet code.

- Apache HTTP Server 2.4 is installed.
- Apache HTTP Server is using /etc/apache2/httpd.conf as its
  configuration file.
- Apache HTTP Server is running.
  - The httpd service is listening on 0.0.0.0:80 for HTTP protocol.
  - The httpd service is listening on 0.0.0.0:443 for HTTPS protocol.
- There is an "admin" user with password "abc123".
```

When we use the word **configuration** in this nutshell, think back to
this configuration. This is, conceptually, the essence of what a
**configuration** is.

### Conclusion

A company will have many different servers, serving many different
applications. Different servers will have different configurations based
on their purpose. Some servers need to have Nginx installed. Some
servers need to have Docker installed. And, some servers need to have
Apache installed.

While it is possible for system administrators to log into each server
and manually configure it, the manual process is cumbersome,
error-prone, and does not scale to large numbers of servers. Companies
get better results when they use an organized system to manage the
configuration of their servers.

The task of effectively managing configurations is termed
**configuration management**.

## Puppet operation

### Resources

**Resources** are the heart of any Puppet configuration. Let's take our
blog server **configuration** above, and break it down in terms of
Puppet **resources**:

- `Apache HTTP Server 2.4 is installed.` This is a `package`
  **resource**.
- `Apache HTTP Server is using /etc/apache2/httpd.conf as its
  configuration file.` This is a `file` **resource**.
- `Apache HTTP Server is running.` This is a `service` **resource**.
- `There is an "admin" user with password "abc123".` This is a `user`
  **resource**.

Every **resource** has a type (e.g. `package`, `file`, `service`, and
`user` resources) that describes what kind of **configuration** it
represents. Every **resource** has attributes that describe the desired
state of the **resource** on the target system.

We can express our blog server configuration in the Puppet language as a
series of **resource declarations**:

...

