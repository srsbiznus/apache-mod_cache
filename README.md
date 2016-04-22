# apache-mod_cache

<pre>
/scripts/easyapache
</pre>

Select mod_cache and rebuild configuration
<pre>
Select ** DEFAULT **
Hit "Tab"
Select [Customize Profile]
Hit "Space"
Select latest Apache version
Hit "Tab", Hit "Space"
Select [Turn off this preference]
Hit "Space"
Select PHP version 5.6.20
Select [Exhaustive Options List] 
Select Cache
Hit "Tab" select [Next Step]
Select [Save and Build]
</pre>

<pre>
vim /usr/local/apache/conf/pagespeed.conf

##Paste this in, replace whatever is in there

<IfModule !mod_version.c>
  LoadModule version_module modules/mod_version.so
</IfModule>

<IfVersion < 2.4>
  LoadModule pagespeed_module modules/mod_pagespeed.so
</IfVersion>

<IfVersion >= 2.4.2>
  LoadModule pagespeed_module modules/mod_pagespeed_ap24.so
</IfVersion>

<IfModule pagespeed_module>
  ModPagespeed on
  ModPagespeedModifyCachingHeaders off
  AddOutputFilterByType MOD_PAGESPEED_OUTPUT_FILTER text/html
  ModPagespeedFileCachePath "/var/cache/pagespeed/"
  ModPagespeedFileCacheSizeKb 102400
  ModPagespeedCreateSharedMemoryMetadataCache "/var/cache/pagespeed/" 102400
  ModPagespeedLRUCacheKbPerProcess 8192
  ModPagespeedLRUCacheByteLimit 16384
  ModPagespeedMemcachedServers localhost:11211
  ModPagespeedLogDir /var/log/pagespeed
</IfModule>
</pre>

<pre>
vim /usr/local/apache/conf/includes/pre_virtualhost_2.conf

## REPLACE APACHE SETTINGS WITH BELOW, leave FCGI settings alone

KeepAlive On
KeepAliveTimeout 2
MaxKeepAliveRequests 1920
<IfModule mpm_event_module>
    StartServers             3
    MinSpareThreads        150
    MaxSpareThreads        250
    ServerLimit            30
    ThreadsPerChild         64
    MaxRequestWorkers      1920
    MaxConnectionsPerChild   0
</IfModule>

SSLProxyEngine on
<IfModule mod_expires.c>
  # turn on the module for this directory
  ExpiresActive on

  # set default
  ExpiresByType image/jpg "access plus 1 month"
  ExpiresByType image/gif "access plus 1 month"
  ExpiresByType image/jpeg "access plus 1 month"
  ExpiresByType image/png "access plus 1 month"
  ExpiresByType text/css "access plus 1 month"
  ExpiresByType text/javascript "access plus 1 month"
  ExpiresByType application/javascript "access plus 1 month"
  ExpiresByType application/x-shockwave-flash "access plus 1 month"
  ExpiresByType text/css "now plus 1 month"
  ExpiresByType image/ico "access plus 1 month"
  ExpiresByType image/x-icon "access plus 1 month"
  ExpiresByType text/html "access plus 600 seconds"
  ExpiresDefault "access plus 2 days"
</IfModule>

<IfModule mod_cache.c>
<IfModule mod_cache_disk.c>
       CacheRoot       "/dev/shm/"
       CacheEnable     disk    /
       CacheDirLevels  2
       CacheDirLength  1
       CacheMaxExpire  5
       CacheIgnoreQueryString Off
       CacheIgnoreNoLastMod On
       CacheHeader on
       CacheLock on
       CacheLockMaxAge 10
       CacheIgnoreHeaders Set-Cookie
</IfModule>
</IfModule>
</pre>

<pre>
vim /usr/local/apache/conf/pagespeed.conf

##Replace contents of file with below


<IfModule !mod_version.c>
  LoadModule version_module modules/mod_version.so
</IfModule>

<IfVersion < 2.4>
  LoadModule pagespeed_module modules/mod_pagespeed.so
</IfVersion>

<IfVersion >= 2.4.2>
  LoadModule pagespeed_module modules/mod_pagespeed_ap24.so
</IfVersion>

<IfModule pagespeed_module>
  ModPagespeed on
  ModPagespeedModifyCachingHeaders off
  AddOutputFilterByType MOD_PAGESPEED_OUTPUT_FILTER text/html
  ModPagespeedFileCachePath "/var/cache/pagespeed/"
  ModPagespeedFileCacheSizeKb 102400
  ModPagespeedCreateSharedMemoryMetadataCache "/var/cache/pagespeed/" 102400
  ModPagespeedLRUCacheKbPerProcess 8192
  ModPagespeedLRUCacheByteLimit 16384
  ModPagespeedMemcachedServers localhost:11211
  ModPagespeedMessageBufferSize 100000
  ModPagespeedLogDir /var/log/pagespeed

</IfModule>
</pre>

<pre>
service httpd stop; service httpd start
service memcached restart
</pre>
