# Magento 2 CE on Platform.sh

This repo provides a starting point for setting up a Magento 2 project on Platform.sh.

## Problem

Most applications require some database connection.  Since part of the point of Platform.sh is that each environment/branch of your project gets its own unique resources, it wouldn't make sense to hardcode the credentials to those resources.  Each environment therefore has it's resources/services encoded into Linux environment variables.  At runtime you pull those credentials out of the environment variables and then build the database/redis/solr/whatever connection string from that.

Every other PHP framework that I know of uses a php file to perform its configuration for stuff like this.  This makes it super simple to pull the connection info out of those environment variables.  The one notable exception is Magento, which uses an XML file to store its configuration.

This presents a problem for Platform.sh.

## Solution

This set of code dynamically genreates the XML config and places it where it needs to go (app/etc/...).  

## Setup

This repo assumes that your Magento codebase is stored in the `web` directory of this repo, so if that's not ok with you you're going to have to reverse some of this code.  

I advise you to step through the `Platform.php` file to get a sense of what's what.

## Composer stuff

Since the Magento 2 download comes with the `vendor` folder already populated with the needed Composer packages you don't need to deal with running `composer install` locally.  They also, however, included the `vendor` folder in the `.gitignore`, which means that when you check your project into Git and push it to Platform.sh, none of your Composer dependencies are there.

This is where our new [Authenticated Composer repositories](https://docs.platform.sh/tutorials/composer-auth.html#authenticated-composer-repositories) feature earns its bacon.  Please read that link for greater understanding.

You'll need to have a Magento account, and then follow [this guide](http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html) to get your credentials for the Magento Composer repo.

The `hooks.build` command present in this repo's `.platform.app.yaml` assumes that you will place your credentials into environment variables named  `magento_user` and `magento_pass`, but feel free to name them whatever and alter the build hook accordingly.

After all this, everything should Just Work.