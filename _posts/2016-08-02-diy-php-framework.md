---
layout: post
title: DoItYourself (DIY) PHP Framework
description: "DoItYourself (DIY) PHP Framework, is a minimal web application framework written in PHP targetting most developers who do not want any fancy sophisticated web framework."
project: true
comments: true
share: true
euler: false
---

## Overview

DoItYourself (DIY) PHP Framework, is a minimal web application framework written in PHP targetting most developers who do not want any fancy sophisticated web frameworks. Frameworks like Laravel, Yii, CodeIgnitor etc. are state of the art featuring so many sophisticated stuffs that any beginning or genious PHP programmer will always feel like he needs to learn the framework before becoming good at either of them. With DIY as the name already suggests, you don't need nothing ... just nothing. All you need to know is concepts of Object Oriented (OO) programming in PHP. The framework however simple, is inspired by Python's Django Framework but will never be that. I want to keep it simple and perfect.

The framework exhibits the following features:

* MVC architecture
* Simple URL routing
* PDO Wrapper which is simply a Database Abstraction Layer
* Easy form manipulation
* Templating engine based on plain old Twig

## Download & Installation

#### Download

* Cloning with Git version control (Latest development copy). Not that this is not recommended.

{% highlight bash %}
    $ cd /path/to/webroot/directory ; git clone https://github.com/nenepadi/diy.git name_of_your_project ; cd diy
{% endhighlight %}

* Download a release [here](https://github.com/nenepadi/diy/releases). This method is recommended.
* Most of the releases come will additional files. These additional files is a binary representation of the source code whichcan be built using Python's cookie-cutter module. Learn more on how to use cookie-cutter [here](https://cookiecutter.readthedocs.io/en/latest/).

#### Installations

After download, you will have to unzip the downloaded file to your webroot directory (i.e if you used recommended method). If you are using a Linux distro do the following in your terminal:

{% highlight bash %}
    $ cd /path/to/extracted/folder 		# it should be in your webroot directory
    ......
    $ php composer.phar install 		# to install framework dependencies
    ......
    $ [sudo] chmod -R 777 public/cache 	# to make the cache directory writable by all
{% endhighlight %}

Windows users however should go to Composer's [homepage](https://getcomposer.org) in order to learn how to use composer on windows.
{: .notice}

## Basic configuration

After installing all dependencies we just need to do a little bit of configuration to get started. First lets edit the __config.php__ file which can be located in the project's root directory. At this point am guessing we have changed the name of the root directory to [name_of_your_project]. Change the last line of the __config.php__ file, which reads:

{% highlight php %}
    <?php
	...
	...

	# Paths. Make sure you put a trailing slash(/) infront of all your paths!!!
	define('BASE_URL', 'http://localhost/~nene/diy/');
{% endhighlight %}

to

{% highlight php %}
    <?php
    ...
    ...

    /*
     * example: if your project is found in /var/www/html/name_of_your_project then
     * define('BASE_URL', 'http://localhost/name_of_your_project');
     *
     * NB: It is assumed that your webserver's document root is /var/www/html
     */

    # Paths. Make sure you put a trailing slash(/) infront of all your paths!!!
    define('BASE_URL', 'base_url_to_your_project');
{% endhighlight %}

This configuration details assumes that you are going to keep the defaults as it is without touching any other path of it.
{: .notice}

After that awesome job that we just did, we would believe that is all, but not so fast. You remember how I said this framework has a lovely syntatic-sugar url routing. Yes, it uses the power of apache's __mod_rewrite__. Open this the __.htaccess__ file which is found in the root directory of the project for editing. Change the following line:

{% highlight apache %}
    RewriteBase /~nene/diy
{% endhighlight %}

to

{% highlight apache %}
    RewriteBase /name_of_your_project
{% endhighlight %}

## Testing whether it works

After all these stress free configurations we are set to go. Open any browser and type what you entered as your base url and you should see the following screen.

![DIY Test Page]({{ site.url }}/images/posts/diy-test.png){: .image-center}

## Documentation

Documentation and tutorials will be out soon. Immediately it is out you will be the first people to know. But in any case just sniffing through the files should tell you more of what could be done. Anyway, continue being a problem solver.

I will soon start a serial of projects using this framework so keep visiting. In the meantime if anyone can contribute mainly to this project I will be of great pleasure.
{: .notice}