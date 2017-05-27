# Ghost blogging nginx configuration for SSL, compression, and caching proxy

This repository includes configuration files to run the Ghost blogging platform with SSL, compression, and a caching proxy to improve the node generated and static content load times.

## SSL

In the example configuration letsencrypt.org is used, with certbot (https://certbot.eff.org/) for provisioning and renewal.

Follow the certbot installation guidance at https://certbot.eff.org/ for nginx and your operating system.

Once you have successfully generated certificates and had them signed by letsencrypt, ensure the renewal of those certificates is automated by added the following crontab job.

```
<random minute> <random hour> * * * certbot renew --quiet --no-self-upgrade
<random minute> <random hour+12> * * * certbot renew --quiet --no-self-upgrade
```

A random minute and hour should be chosen to help prevent letsencrypt servers getting over loaded at popular times of day. For example:

```
43 5 * * * certbot renew --quiet --no-self-upgrade
27 17 * * * certbot renew --quiet --no-self-upgrade
```

## Compression

Gzip compressiong has been enabled to compress html, javascript, xml etc. This configuration can be found in nginx.conf

Most of the settings are default but I've added `gzip_min_length 256;` as I think it's not worth compressing files less than 256 bytes.
 
## Caching

In nginx.conf you can see that proxy_cache_path has been added to point at /tmp/ghostcache:

```
proxy_cache_path /tmp/ghostcache levels=1:2 keys_zone=ghostcache:100m max_size=1g inactive=1y;
``` 

Here there are some parameters which you change for your specific installation. The max_size is set to 1 gigabyte, this should be more than enough for most blogs. inactive is set to 1 year, files won't be removed from the cache for a year, this is fine for blogs where previous posts don't change.

Then in the ghost-ssl-cache site configuration there are a whole host of proxy configuration explained through comments in the configuration file.

Static assets such as theme assets and images are serves directly by nginx. You need to specify your theme folder in the assets location.
