
Multisite Module (WIP)

Domains or Subdomains are now configured via config.php

```
$config->MultisiteDomains = array(
    "dev.domain.com" => array( // domain name can be used to map to root page
            "root" => "www.domain.com", // page name for the root page
            "http404" => 27
        ),
    "dev.domain2.com" => array(
            "root" => "www.domain2.com",
            "http404" => 5332
        ),
);
```

