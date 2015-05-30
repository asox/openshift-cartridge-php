# Openshift PHP-FPM Cartridge
[PHP-FPM](http://php.net/manual/en/book.fpm.php) cartridge for [Openshift](https://www.openshift.com/).
Based on [boekkooi/openshift-cartridge-php](https://github.com/boekkooi/openshift-cartridge-php)

## Installation
You can add this cartridge to your application using:
```BASH
rhc cartridge add -a myapp http://cartreflect-claytondev.rhcloud.com/github/asox/openshift-cartridge-php
```

## Configuration
For the best result with this cartridge you are adviced to create a `.openshift/action_hooks/build` and add the following to it:
```BASH
#!/bin/bash
set -e

# Build PHP extra's
# Update conf/ini files and install pecl extensions
${OPENSHIFT_PHP_DIR}/bin/control build
```

### php.ini & php-fpm.conf
If you have created `.openshift/action_hooks/build` you can fully customize the PHP configuration.
In your application create the following directories:
```
.openshift/cli/
.openshift/fpm/
```

In the `cli` directory create the `php.ini.erb` file and copy the content from [`conf/php.ini.erb`](https://github.com/asox/openshift-cartridge-php/blob/master/conf/php.ini.erb) into it.
Now you can customize the cli php configuration.

In the `fpm` directory create the `php.ini.erb` file and copy the content from [`conf/php-fpm.ini.erb`](https://github.com/asox/openshift-cartridge-php/blob/master/conf/php-fpm.ini.erb) into it.
Also create the `php-fpm.conf.erb` file and copy the content from [`conf/php-fpm.ini.erb`](https://github.com/asox/openshift-cartridge-php/blob/master/conf/php-fpm.conf.erb) into it.
Now you can customize the php-fpm configuration.

### PECL
If you have created `.openshift/action_hooks/build` you can create the `.openshift/php-pecl.txt` to auto install pecl extensions.
This file must constain have a pecl extension name and version per line for example:
```
apcu 4.0.7
mongo 1.6.5
```

### Phalcon
There is special support for [phalcon](http://phalconphp.com/) you can simply install it by adding the following to your `.openshift/php-pecl.txt` file.
```
phalcon 1.3.4 --enable-phalcon
```
Don't forget to change your `.openshift/nginx.conf.erb` according to the [phalcon nginx installation notes](http://docs.phalconphp.com/en/latest/reference/nginx.html).

### Composer
Composer is installed by default and can be used by simply ssh to your application and typing in `composer`.

## Versions
Currently this cartridge has the following versions:
- PHP 5.6.6

If you need another version you can compile it yourself.

### Compiling a new version
To compile a new version you will first need a openshift application.
```BASH
rhc create-app php http://cartreflect-claytondev.rhcloud.com/github/asox/openshift-cartridge-php
```

Now clone the repository and create a `php` folder. Now copy the `usr/compile` directory from [this](https://github.com/asox/openshift-cartridge-php) repository.
Now set the versions you need to compile in the `php/compile/versions` file. Commit and push the application repository.

SSH into your app and go to the compile folder (`cd ${OPENSHIFT_REPO_DIR}/php/compile`) and start compiling by running the following commands:
```BASH
./libs
./php
./libs_package
```
Once compiling is done you can download the `php-{version}.tar.gz` from you application.
Extract the `php-{version}` from the archive and place them into the `openshift-cartridge-php/usr` folder.
Last but not least edit the `openshift-cartridge-php/manifest.yml` and add the versions.

All done just commit and push to your `openshift-cartridge-php` repo and use:
```BASH
rhc cartridge add -a myapp http://cartreflect-claytondev.rhcloud.com/github/<user>/openshift-cartridge-php
```

## Updates
Updating this catridge is not as easy as I would like because openshift online won't allow updates for downloaded cartridges.
To update the cartridge you can do the following:
```BASH
rhc cartridge remove -a myapp --confirm  php
rhc cartridge add -a myapp http://cartreflect-claytondev.rhcloud.com/github/<user>/openshift-cartridge-php
```
This will remove the old version and install the latest version.
