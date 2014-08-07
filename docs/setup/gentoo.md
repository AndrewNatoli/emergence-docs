## Configure build optoins

This guide will assume that you are starting from a clean Gentoo install, following the official Gentoo Handbook.

We will be installing [nginx][nginx], [PHP][php], [MySQL][mysql], and [Node.js][nodejs].

All documentation examples will show text editing with `nano` to follow Gentoo handbook conventions. You may of course use any editor you wish.

### USE flags
Before we `emerge` everything, we need to configure which features should be included during compilation:

```language-bash
user@hostname ~ $ sudo nano -w /etc/portage/package.use
```

Add these lines to `/etc/portage/package.use`:
<pre class="language-bash file-contents">
    <p><b class="portage-package">www-servers/nginx</b> <b class="portage-on">ssl</b> <b class="portage-on">nginx_modules_http_fastcgi</b> <b class="portage-on">nginx_modules_http_flv</b> <b class="portage-off">-nginx_modules_http_geo</b> <b class="portage-off">-nginx_modules_http_memcached</b> <b class="portage-off">-nginx_modules_http_referer</b> <b class="portage-off">-nginx_modules_http_proxy</b> <b class="portage-off">-nginx_modules_http_scgi</b> <b class="portage-off">-nginx_modules_http_ssi</b> <b class="portage-off">-nginx_modules_http_uwsgi</b></p>
    <p><b class="portage-package">dev-lang/php</b> <b class="portage-on">ctype</b> <b class="portage-on">curl</b> <b class="portage-on">filter</b> <b class="portage-on">gd</b> <b class="portage-on">imap</b> <b class="portage-on">json</b> <b class="portage-on">mysqli</b> <b class="portage-on">simplexml</b> <b class="portage-on">truetype</b> <b class="portage-on">xml</b> <b class="portage-on">xmlreader</b> <b class="portage-on">xmlrpc</b> <b class="portage-on">zip</b> <b class="portage-on">soap</b> <b class="portage-on">fpm</b></p>
</pre>

<div class="info" markdown="1">
#### /etc/portage/package.use
If this file doesn't already exist, then the directory probably doesn't either, so run `sudo mkdir /etc/portage/` to make the directory.

Once you have the directory, follow the instructions above again. Your text editor should create the file for you.
</div>

### Unmask Node.js for 64-bit
If you're running the amd64 version of Gentoo, you'll need to unmask Node:

```language-bash
user@hostname ~ $ sudo nano -w /etc/portage/package.keywords
```

Add these lines to `/etc/portage/package.keywords`:
<pre class="language-bash file-contents">
    <p>net-libs/nodejs</p>
</pre>

### Set PHP target to 5.3
This tells Gentoo exactly which PHP major version you want to use when compiling PHP and its extensions:

```language-bash
user@hostname ~ $ sudo nano -w /etc/portage/make.conf
user@hostname ~ $ sudo eselect php set fpm 1
```

Add these lines to `/etc/make.conf`:
<pre class="language-bash file-contents">
    <p>PHP_TARGETS="php5-3"</p>
</pre>


## `emerge` server binaries
Now let's compile. If you're on a clean install, this will take some time:

```language-bash
user@hostname ~ $ sudo emerge -av nginx php mysql nodejs pecl-apc pecl-imagick
```


## Increase shared memory limit
Gentoo comes with a low limit of 32MB for shared memory. Emergence relies heavily on APC caching and needs kernel.shmmax increased to a more flexible amount. We'll use 128MB:

```language-bash
user@hostname ~ $ echo -e "kernel.shmmax = 268435456\nkernel.shmall = 65536" | sudo tee -a /etc/sysctl.d/60-shmmax.conf
user@hostname ~ $ sudo sysctl -w kernel.shmmax=268435456 kernel.shmall=65536
user@hostname ~ $ echo -e "apc.shm_size=128M" | sudo tee -a /etc/php/fpm-php5.3/ext/apc.ini
```


## Install Emergence from GitHub
Clone Emergence into your home directory, then use `npm` to install the package and its dependencies:

```language-bash
user@hostname ~ $ git clone https://github.com/JarvusInnovations/Emergence ~/Emergence
user@hostname ~ $ cd ~/Emergence
user@hostname ~/Emergence $ sudo npm install -g
```


## Start Emergence
`npm -g` installed the kernel's startup script to `/usr/bin/emergence-kernel`. You can now launch it manually, or install the init script:

```language-bash
user@hostname ~ $ sudo wget http://emr.ge/dist/gentoo/init.d -O /etc/init.d/emergence-kernel
user@hostname ~ $ sudo chmod +x /etc/init.d/emergence-kernel
user@hostname ~ $ sudo rc-update add emergence-kernel
user@hostname ~ $ sudo /etc/init.d/emergence-kernel start
```


## Create a site
You can now open your web browser to take control of Emergence and create your first site: [http://127.0.0.1:9083](http://127.0.0.1:9083). If you're installing to a remote machine, replace 127.0.0.1 with your remote IP or hostname.

When prompted, log in with the username and password <kbd>admin</kbd> / <kbd>admin</kbd>.


## Secure your installation
Once you confirm that you are able to access the control panel, use the `htpasswd` tool provided in npm to delete the default admin account and create your own. Then restart the Emergence kernel to apply the changes:

```language-bash
user@hostname ~ $ sudo npm install -g htpasswd
user@hostname ~ $ sudo htpasswd -D /emergence/admins.htpasswd admin
user@hostname ~ $ [[[sudo htpasswd -s /emergence/admins.htpasswd]]] myusername
user@hostname ~ $ sudo restart emergence-kernel
```



[nginx]: http://nginx.org/
[php]: http://php.net/
[mysql]: http://mysql.com/
[nodejs]: http://nodejs.org/