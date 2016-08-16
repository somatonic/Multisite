# Multisite module for Processwire (WIP)

The basic module was written by Antti Peisa and forked by Philipp "Soma" Urlich (_"there's not much left from the original module, but the basic concept is the same"_).

* [Github: Multisite](https://github.com/somatonic/Multisite) by Philipp "Soma" Urlich 
* [Github: Multisite](https://github.com/apeisa/Multisite) by Antti Peisa – **deprecated**
* [ProcessWire Forum](https://processwire.com/talk/topic/1025-multisite/)

**Changelog**

* support Multilanguage (module LanguageSupportPageNames must be installed)
* support for correct view Actions from admin
* correct urls within admin
* modified `localUrl()` to work correctly, coming from [LanguageSupportNames](http://processwire.com/api/multi-language-support/multi-language-urls/) module

## What does this module do?

It allows you to run multiple sites with different domains from a single install, using the same database.
While you can easily do _"subsites"_ like `www.domain.com/campaign/`, this allows you to turn that into `www.campaign.com`.
This is nice stuff when you have multiple simple sites which all belong to the same organisation and which the same people maintain.

## Isn't there multisite support in core?

Yes, [kind of](https://processwire.com/api/modules/multi-site-support/). It is very different from this. It allows you to run multiple pw-installations with shared core files (/wire/ folder).
This is totally different: this is a single pw-installation which has multiple sites running from different domains.

## How to use it?

Just create pages with names like `www.campaigndomain.com` unter the root page and add these in the `config.php` via an array in `$config->MultisiteDomains`.

This allows for different domain configurations on different environments like dev and live stage, and since it's not in DB (via the module config) it can be easily transfered with a dump without worrying to overwrite or change the settings.
 
Also there's no need to change the domain "root" pages name, as it's not directly coupled to the requesting domain. 
So you only change the array keys (=domain).

### Add MultisiteDomains

#### Live environment

```php
$config->MultisiteDomains = array(
    "domain1.com" => array( // domain name can be used to map to root page
            "root" => "www.domain1.com", // page name for the root page
            "http404" => 27
        ),
    "domain2.com" => array(
            "root" => "www.domain2.com",
            "http404" => 5332
        )
);
```

#### Override for dev environment

```php
$config->MultisiteDomains = array(
    "dev.domain1.com" => array( // domain name can be used to map to root page
            "root" => "www.domain1.com", // page name for the root page
            "http404" => 27
        ),
    "dev.domain2.com" => array(
            "root" => "www.domain2.com",
            "http404" => 5332
        )
);
```

### Add urls to httpHosts depending on environment

#### Live environment

```php
$config->httpHosts = array('domain.com', 'domain2.com');
```

#### Dev environment

```php
$config->httpHosts = array('dev.domain.com', 'dev.domain2.com');
```

#### Automatic httpHosts setup

```php
$config->MultisiteDomains = […];
$config->httpHosts = array_keys($config->MultisiteDomains);
```

### Domains

Make sure **domain2.com** points to the same server (A record or CNAME) as **domain1.com** and so on.
And on the server side you have to make sure that **domain2.com** is pointing to the same directory as **domain1.com**.

### Build the page tree

```
- Web (PW root page, I call it always "Web" since it isn't the homepage anymore)
   - www.domain1.com (primary site home)
     - Example Page
     - 404 Page
     - Contact Page
   - www.domain2.com (a second site home)
     - 404 Page
   ...

```

### Good to know: some variables

```php
// get current domain
echo $modules->get('Multisite')->domain; 
[dev]  'dev.domain1.com'
[live] 'domain1.com'

// get current site root
$siteRoot = $page->rootParent;
echo $siteRoot->name // root page name
[dev]  'www.domain1.com'
[live] 'www.domain1.com'

// use it as navigation starting point
foreach ($siteRoot->children as $child) {
  echo $child->title;
}
```

### TroubleShooting

#### Wrong page tree structure

```
- Homepage (main site home)
   - Example Page
   - 404 Page
   - Contact Page
   - www.domain2.com (a second site home)
     - 404 Page
   ...

```

But this wasn't ever recommended and it can lead to complications.

#### Domains are missing insite httpHosts

Check that all domains for the specific environment are included inside `$config->httpHosts`.

#### DNS Record is not set properly
 
The DNS Record of all domains must point to the same server.

#### Domains point to a wrong directory

All domains must point to the same directory.

#### Multi-language Domains are not working

Make sure the **LanguageSupportPageNames** module is installed.

Edit the root page in the example above called *Web*  and add language paths there. For example `en`, `de` and `fr` depending on the added languages.

### Important

Since the whole concept is all a pretty hack, I found that it comes with some complications that can't be solved in an elegant way. So for example the biggest issue is that you can't crosslink pages via the RTE Link plugin, since it doesn't know about Multisite. So you'll end with wrong URL's when for example you link from a page of one site to a page of another site. If that's an issue it's still possible to copy the ProcessPageEditLink.module and modify the root parent for the page tree select. I'd be glad to help out with an example there.

Again since this module is pretty much a hack, I'm not officially supporting and releasing this module. Use at your own risk. We use it in various projects now and while it works fine with all it's little drawbacks, this version is little more solid.
 
I would rather like to see if there's a way for a more integrated and supported way in core. But I'm not even sure how this could work out. Ryan may has some ideas or maybe thinks this isn't something PW could support. - Note that there's [multisite core support](https://processwire.com/api/modules/multi-site-support/), but it's for different DB's and "site" folders, but that's a different case altogether.

