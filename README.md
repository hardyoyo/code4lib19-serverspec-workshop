# Wait, what? Getting your bearings with ServerSpec
## Preconference Workshop, Code4Lib 2019, San Jose, CA

Abstract: Library developers change jobs, we’re always the newbie.
[ServerSpec](https://serverspec.org/) is a fantastic tool for keeping track of
exactly what is supposed to be running on which server. Chances are such tests
don’t exist in your current workplace; chances are such tests would be
significantly helpful to your coworkers. This is a tool that should be in
your box.

Participants will learn to write simple yet efficient ServerSpec tests, how to
organize them and invoke them for different machines, ways to approach managing
many tests, and ways to incorporate them into project life cycles.

We will also briefly talk about other infrastructure testing frameworks; why
you would consider them instead of ServerSpec, and why you will probably want
to use ServerSpec anyway, warts and all.

Pre-requirements: participants should bring a notebook computer, with
[Virtualbox](https://virtualbox.org/) and [Vagrant](https://vagrantup.com/) already installed, if you wish to follow along with the
presented material. Interested spectators are welcome, however, you’ll get
more out of the workshop if you participate. You’ll get the most out of this
workshop if your day to day work has you installing services on servers (or
you do so in your spare time for your own projects). You should probably be
comfortable on a command line, and with the Linux bash shell. A passing
familiarity with RSpec would help but isn’t required.

## Vagrant Plugin Requirements and Recommendations

Vagrant has a robust community of plugin developers, and some of the plugins are quite nice. [Installing a Vagrant plugin](https://www.vagrantup.com/docs/plugins/usage.html) is simple.

> #### TLDR, just tell me what to install...
>
> ... OK, fine, run this command to install a the two plugins we MUST have to follow along with this workshop:
>
> `vagrant plugin install vagrant-env vagrant-spec`

* [Vagrant-ENV](https://github.com/gosuri/vagrant-env) is *required*, in order to boot our workshop VM.
* [Vagrant-Spec](https://github.com/hashicorp/vagrant-spec) is *required* in order to work with ServerSpec on our workshop VM.

The following Vagrant plugins are not required, but they do make using Vagrant more enjoyable.

* [Vagrant-Hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater) (or any of the [local domain resolution plugins](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins#local-domain-resolution) you might prefer)
* [Vagrant-VBGuest](https://github.com/dotless-de/vagrant-vbguest) - *Highly Recommended for VirtualBox* as it keeps VirtualBox Guest Additions up to date
* [Vagrant-Cachier](https://github.com/fgrehm/vagrant-cachier) - Caches packages between VMs. (Project now unmaintained, see URL)
* [Vagrant-Notify](https://github.com/fgrehm/vagrant-notify)


* Slides: [![GitPitch](https://gitpitch.com/assets/badge.svg)](https://gitpitch.com/hardyoyo/code4lib19-serverspec-workshop)
