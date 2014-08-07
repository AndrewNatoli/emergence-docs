The Emergence kernel is a Node.js daemon responsible for:

 - Initializing a machine on first run
 - Loading the configuration for all sites hosted on the current machine
 - Providing an online management API for creating and reconfiguring sites
 - Detecting available services (nginx, PHP-FPM, MySQL)
 - Dynamically provisioning and managing services for each site as specified in the site's configuration
 - Monitoring service processes and scripting reactions to them

The `emergence-kernel` command launches the kernel and is available on the system after installing the npm package as described in the
[setup docs](/docs/setup). Typically it will be started automatically by the system startup script installed for your specific
distribution.

Any existing startup scripts installed for services like MySQL should be deactivated, unless you have specifically configured them not
to use any network ports that the Emergence-managed instances will need to listen on. The kernel will start first and handle launching
isolated instances of these services to fulfill your sites' configurations.

## Directory Layout

 - `/emergence`: The root of all things Emergence. Everything is in here, nothing outside.
 - `/emergence/services`: Files relating to provisioned services.
 - `/emergence/sites`: Configuration and data for all the sites on this server, with each site in its own sub-directory.