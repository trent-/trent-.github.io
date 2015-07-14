---
layout: post
title:  Setting up a GitHub hosted blog on Ubuntu
date:   2015-07-14 12:41:00
---

Well, for my first post on my GitHub, Jekyll based blog, I thought it'd be fitting to document how I got up and going on my Ubuntu machine. Every repository or user on GitHub has the ability to host a site using the Jekyll technology so it's worth installing Jekyll on your machine so that you can see how your site would behave before pushing it to GitHub.

GitHub includes a basic guide on getting set up here: <https://help.github.com/articles/using-jekyll-with-pages/>

The first step in this guide is to install Ruby. This mentions that the minimum required version is 2. You will run into problems here if you are running Ubuntu 14.04 (the latest LTS at the time of writing) because the default Ruby is version 1.9 (note: on a later version of Ubuntu - I tested on Ubuntu 15.04 - the default Ruby is 2.1.2). Fortunately, version 2 of Ruby is available in the packages through the ruby2.0 package. So start by installing ruby2.0:

```bash
sudo apt-get install ruby2.0
```

If you then run the command `ruby --version` you will see that the output is pointing to version 1.9. You can also see that this is pointing to 1.9 with:

```bash
$ which ruby
/usr/bin/ruby
$ ls -Alt /usr/bin/ | grep ruby
-rwxr-xr-x 1 root   root        6304 Nov 20  2014 ruby1.9.1
-rwxr-xr-x 1 root   root        6264 Nov 20  2014 ruby2.0
lrwxrwxrwx 1 root   root           9 Feb 15  2014 ruby -> ruby1.9.1
```

So, at this point we can either update the symbolic link of `ruby` to point to `ruby2.0`. However, instead I am going to use the `update-alteratives` command to specify which ruby to point to.

```bash
$ sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby2.0 1
update-alternatives: warning: forcing reinstallation of alternative /usr/bin/ruby2.0 because link group ruby is broken
$ ls -Alt /usr/bin/ | grep ruby
lrwxrwxrwx 1 root   root          22 Jul 13 00:14 ruby -> /etc/alternatives/ruby
-rwxr-xr-x 1 root   root        6304 Nov 20  2014 ruby1.9.1
-rwxr-xr-x 1 root   root        6264 Nov 20  2014 ruby2.0
$ ruby --version
ruby 2.0.0p384 (2014-01-12) [x86_64-linux-gnu]
```

Looking in /usr/bin, you will also see that `gem` follows a similar pattern, so we will want to do the same for that.


```bash
$ which gem
/usr/bin/gem
$ ls -Alt /usr/bin | grep gem
-rwxr-xr-x 1 root   root         547 Nov 20  2014 gem1.9.1
-rwxr-xr-x 1 root   root         545 Nov 20  2014 gem2.0
lrwxrwxrwx 1 root   root           8 Feb 15  2014 gem -> gem1.9.1
trent@birroth:~$ gem --version
1.8.23
trent@birroth:~$ sudo update-alternatives --install /usr/bin/gem gem /usr/bin/gem2.0 1
update-alternatives: using /usr/bin/gem2.0 to provide /usr/bin/gem (gem) in auto mode
trent@birroth:~$ gem --version
2.0.14
```

The next software you'll want to install is `bundler` (as advised in the GitHub documentation linked to earlier on) which is a package manager for Ruby. This is installed with gem:

```bash
sudo gem install bundler
```

The final part is to install the `github-pages` gem. To do this, you will need to grab some dependencies for the build to succeed:

```bash
sudo apt-get install ruby2.0-dev zlib1g-dev nodejs
```

Then you can either install by using bundler. Create a file named Gemfile in your project directory (example below) and use bundler to install all requirements that are defined with the command `bundle install`.

```ruby
source 'https://rubygems.org'
gem 'github-pages'
```

Or just run the command `sudo gem install github-pages`.

If all goes well, you should be able to create a jekyll site and serve it up on your machine. This is done very simply by running the followings commands on the command line:

```bash
jekyll new pathtoblog
cd pathtoblog
jekyll serve
```

That will install a default theme. The other option you have is to grab one of the many other themes available, and customise it to suit your needs.

There is a site dedicated to Jekyll themes: <http://jekyllthemes.org/> or you can browse the Jekyll wiki on GitHub: <https://github.com/jekyll/jekyll/wiki/Themes>

Now, go and get blogging. You'll likely want to refer back to the Jekyll documentation: <http://jekyllrb.com/docs/home/>
