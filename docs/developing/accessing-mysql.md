The Emergence Kernel creates a standalone instance of MySQL so as not to interfere with any existing setup on a server and to produce
a consistent configuration across distributions. This means that default connection settings or existing users on your system will not enable
you to connect to your Emergence-managed MySQL instance via external tools like the MySQL command line client or phpMyAdmin.

## Obtaining root login credentials
When the Emergence Kernel starts for the first time in initializes a new MySQL data directory under `/emergence/services/data/mysql/`. A root
password for this instance is randomly generated and stored in `/emergence/config.json` -- the password is plaintext so take care that this file
remains owned and only readable by root. By looking inside this file from your root account you can find the root password for
Emergence's MySQL instance:

```language-bash
user@hostname ~ $ sudo cat /emergence/config.json
```

## Connecting via `mysql` command-line client
By default, the MySQL instance configured by the Emergence Kernel will not bind to a network port, and only be reachable via UNIX socket. Use
the following command to open a connection as the root user to your MySQL instance, and then use the password discovered from
`/emergence/config.json` above when prompted.

```language-bash
user@hostname ~ $ mysql -u root -p -S /emergence/services/run/mysqld/mysqld.sock 
```

## Connecting via phpMyAdmin or other remote clients
If your phpMyAdmin instance is running on the same server as emergence, you can configure it to connect via the UNIX socket as described
above. Running phpMyAdmin on the same server presents a challenge though since emergence cannot host phpMyAdmin but occupies the default
HTTP port. It is possible, but requires manually configuring an HTTP+PHP instance outside emergence that does not listen on any of the same ports.

Instead, it is simpler to install phpMyAdmin on a separate server and then configure it to connect to one or more external emergence servers over
a TCP/IP socket. To configure emergence's MySQL instance to be available via a TCP/IP socket, edit `/emergence/config.json` and add a new attribute
called `"bindHost"` to the `services.plugins.sql` section:

<pre class="shell file-contents preserve-whitespace">
{
    <em>/* ... */</em>
    "services": {
        "plugins": {
            <em>/* ... */</em>
            "sql": {
                <em>/* ... */</em>
                <strong class="selection-target">"bindHost": "127.0.0.1"</strong>
            },
            <em>/* ... */</em>
        }
    }
}
</pre>

In this example, we bind MySQL only to the loopback address, so only clients running from the same machine or using an SSH tunnel will be able to connect.
An SSH tunnel is the most secure way to connect from a remote machine, but you could also set bindHost to a public or local network IP address, 
or to the special value `"0.0.0.0"` which will cause MySQL to bind to **all** available network addresses. Binding to all available network addresses
is the simplest solution, but may present a security risk if used outside a closed network.

## Creating your own users
Once connected with the root account, you may create new MySQL user accounts manually for other administrators or for automated processes like
backup scripts. Just be sure that the username you select does not collide with the "handle" field for any existing or future sites created from
the Emergence Kernel.