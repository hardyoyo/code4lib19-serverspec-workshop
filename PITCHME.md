# Wait, what? Getting your bearings with ServerSpec

Hardy Pottinger

Digital Library Software Developer, UCLA Library

@fa[twitter] @hardy.pottinger

@fa[envelope] hpottinger@library.ucla.edu

Note:
Hi, welcome, thanks for coming.

---
# We are constantly learning more about our environment
* Developers shop jobs a lot
* Have you seen the mailing lists?
* We change jobs a lot
* Our jobs change a lot all on their own
* @css[just-code](We are always the newbie)

Note:
More spearker notes here.

---
![connections of ideas](assets/images/_absolutely_free_photos_original_photos_connection-of-ideas-3600x2542_26127.jpg)

Note:
So, here's a story I read a while back, maybe it'll sound familiar? You come in at a new job, you have the initial “Hey newbie, here’s your new network. You have web machines, databases, app servers, caching servers, configuration management and other boxes. We have process monitoring ...em... nagios... heh... and yes we have configuration management where you have to do code reviews to get anything out to production.” As a newbie I’m excited *this is great* but here comes the kicker. Your  trainer continues, “Ok, lets walk through each machine. There are 10 web servers, 9 of them are called www1 through www9, and one called paco.” You quickly grab a piece of paper, “Paco?”, “Yes the admin before you didn’t like standard names, he wanted to give machines ‘personality’, and we had a change after him, so we have that one off.”

---
# Why write tests?
## With ServerSpec or any similar tool?
* Tests are documentation
* Your team may or may not survive
* Provisioning tools come and go
* No matter what happens to the tools or your team, these tests will persist as documentation of your intentions and proof that the service is configured as you expected

Note:
ServerSpec is one tool for for testing infrastructure, it's not the only option,
but it's common enough that if you're looking to write an infrastructure test
you should consider using it.

What is Serverspec?

---
# ServerSpec
* Extension of RSpec
* Is a Ruby gem
* Is a great way to force yourself to think about your intentions before you provision a new service
* Is a great way to get to know your existing services

Note:
It's this last bullet that I want to focus on today. TDD is cool and I will point
out ways you can incorporate TDD to learn ServerSpec. But, the focus will be less
on creating new things and more on figuring out what has already been built.

---
# Software Reuse
## spec spec spec...
* RSpec
* ServerSpec
* DockerSpec
* SpecInfra
* ¯\\_(ツ)_/¯
Note:
Just to give you a heads up, you'll hear me mention a few different words with
"spec" in them, I will eventually show you an image that explains how most
of them interrelate, however, it's way too much detail right now. Just trust me,
this is normal Ruby stuff. Basic software reuse. Cool?

---
# Installing ServerSpec
* It’s a Ruby gem, you’ll need Ruby 2.0.x+ installed
@css[just-code](sudo gem install serverspec)
* You will need Rake installed, too:
@css[just-code](sudo gem install rake)
* You will need SSH access to the servers you want to check
* You will **not** need Sudo on these servers, though Sudo does make things slightly easier

Note:
Sudo: I will point out areas where things may get a little dicey if your test logs in as a normal user. It all works, but you have to adjust some tests for unprivileged users.
After you install ServerSpec, you can start off with the serverspec-init command.

---
```
$ serverspec-init
Select OS type:
  1) UN*X
  2) Windows
Select number: 1
Select a backend type:
  1) SSH
  2) Exec (local)
Select number: 1
Vagrant instance y/n: n
Input target host name: waitwat
 + spec/
 + spec/waitwat/
 + spec/waitwat/sample_spec.rb
 + spec/spec_helper.rb
 + Rakefile
 + .rspec
```
@[14](let's look at this sample_spec.rb)
---
```
require 'spec_helper'
describe package('httpd') do
  it { should be_installed }
end

describe service('httpd') do
  it { should be_enabled }
  it { should be_running }
end

describe port(80) do
  it { should be_listening }
end

describe file(/etc/httpd/conf/httpd.conf) do
  it {should be_file}
  it {should contain "ServerName my-server-name"}
end
```
@[1](spec_helper)
@[2-4](package resource)
@[6-9](service resource)
@[11-13](port resource)
@[15-18](file resource)

Note:
If you've worked with Rails before, you've seen a "helper", it's a way to pull
out some complexity to improve the readability of your code. For ServerSpec,
it helps improve the readability of your tests. The gist is: don't forget about
this file... you'll probably need to dig into it to make things work. But we will
skip it for now. Instead, let's look at all the other pieces we get.
---
# Resources
https://serverspec.org/resource_types.html
* packages
* services
* ports
* files
* commands
* users and groups
* cron

---
# run the tests
* RSpec tests usually go in a folder called @css[just-code](spec)
* calling ServerSpec is exactly the same as any other RSpec test

@css[just-code](bundle exec rspec spec)
or
@css[just-code](rspec spec)

* this will run all the tests in the spec folder.
* you can also use a Rakefile to automate more complex tests


---?terminal=sessions/serverspec_samvera_demo.cast&color=white&title=UCLALibrary/serverspec-samvera

---
# scaling up to more than one server
https://tinyurl.com/uclalibrary-serverspec-samvera

---
# spec_helper
```
require 'serverspec'
require 'pathname'
require 'net/ssh'
require 'yaml'

base_spec_dir = Pathname.new(File.join(File.dirname(__FILE__)))

Dir[base_spec_dir.join('shared/**/*.rb')].sort.each{ |f| require f }

set :backend, :ssh
set :disable_sudo, true

set :path, '/usr/sbin:$PATH'

properties = YAML.load_file(base_spec_dir.join('properties.yml'))

options = Net::SSH::Config.for(host)
options[:user] = 'deploy'
host = ENV['TARGET_HOST']

set :host,        options[:host_name] || host
set :ssh_options, options

set_property properties[host]
```
Note:
The spec_helper file is a way to pull out some complexity to improve the
readability of your code. For ServerSpec, it helps improve the readability of
your tests.

---
# Use a Rakefile
* automate the running of complex tests
* Rake is a software task management and build automation tool
* Similar to Make
* Written in Ruby

---
# sharing code
https://serverspec.org/advanced_tips.html
https://tinyurl.com/uclalibrary-serverspec-samvera

---
![ServerSpec Components](assets/images/serverspec_components.jpg)
ServerSpec Components, adapted from ["Introduction to Test-Driven Docker Development,"](https://entwickler.de/online/development/docker-test-driven-development-b-170207.html) by Peter Roßbach,  Wednesday, August 12, 2015, [Entwickler.de](https://entwickler.de/)

---
# gotchas
* you'll need to be sure the `ss` command is available on the test target
  * this isntalled by default on RHEL
  * for Ubuntu, you'll need to install the iproute2 package
* you'll need to be sure `/usr/sbin` is in the path, if your test target is RHEL
  * you can set the `:path` in spec_helper


---
# containers? Docker?
* many options are available, worth researching
* DockerSpec: https://github.com/zuazo/dockerspec

---?terminal=sessions/docker-cantaloupe-demo.cast&color=white&title=UCLALibrary/docker-cantaloupe



---
# Thanks! Questsions?

Slides: [github.com/hardyoyo/code4lib19-serverspec-workshop](https://github.com/hardyoyo/code4lib19-serverspec-workshop)
Credits:

Story of the newbie:
https://jjasghar.github.io/blog/2013/07/12/serverspec-the-new-best-way-to-learn-and-audit-your-infrastructure/
Note:
Thanks for coming! Any questions?
