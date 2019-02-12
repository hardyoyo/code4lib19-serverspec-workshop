# Wait, what? Getting your bearings with ServerSpec

Hardy Pottinger

Digital Library Software Developer, UCLA Library

@fa[twitter] @hardy.pottinger

@fa[envelope] hpottinger@library.ucla.edu

Note:
Hi, welcome, thanks for coming. The title of this talk is Wait, what? Getting your bearings with ServerSpec, I'm Hardy Pottinger, I'm one of the Digial Library Software Developers at UCLA Library. The main theme of this talk is...

---
# We are constantly learning about our environment
* Developers shop jobs a lot
* Have you seen the mailing list?
* We change jobs a lot
* Our jobs change a lot all on their own
* @css[just-code](We are always the newbie)

Note:
We are constantly learning about our environment. Seriously, have you see the mailing list? And our jobs change all on their own, all the time. We are always the newbie.

---?image=assets/images/_absolutely_free_photos_original_photos_connection-of-ideas-3600x2542_26127.jpg&size=contain
@snap[north]
@css[text-black](always the newbie...)
@snapend
Note:
Here's a story I read a while back. First day, new job your trainer has been giving you notes all day, and then they go, “Ok, lets walk through each machine. There are 10 web servers, 9 of them are called www-something, and one called paco.” You quickly grab a piece of paper, “Paco?”, “Yep the admin before you didn’t like standard names, wanted to give machines ‘personality’, so we have that one off.” Sound familiar?

---
# Why write tests?
## With ServerSpec or any similar tool?
* Tests are documentation
* Your team may not survive
* Tools come and go
* No matter what happens to the tools or your team, tests will persist as documentation of your intentions and proof that the service is configured as you expected

Note:
Why write tests? How can that possibly help in this situation? And why use ServerSpec?
Well, honestly, ServerSpec is just one tool for for testing infrastructure,
it's not the only option, but it's common enough that if you're looking to write
an infrastructure test you should consider using it. As to why, tests are
documentation. And let's be honest, your team may not survive the next re-org.
Tools come and go. But if you write tests in ServerSpec, your tests will remain
accessible to anyone reading the code. I'll show you what I mean in a bit. But
first...

What is Serverspec?

---
# ServerSpec
* Extension of RSpec
* Is a Ruby gem
* Is a great way to force yourself to think about your intentions before you provision a new service
* Is a great way to get to know your existing services

Note:
What is ServerSpec? It's an extension of RSpec, which is a common testing framework
for Ruby. It's also a way to get you to think about your goals before you start
working with a service. And it's also a way to get to know what you already have.
Its this last point that I want to focus on today.

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
* Youll need Rake installed, too:
@css[just-code](sudo gem install rake)
* You'll need SSH access to the servers you are testing
* You'll **not** need Sudo on these servers, though Sudo does make things slightly easier

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
# Run the tests
* RSpec tests usually go in a folder called @css[just-code](spec)
* calling ServerSpec is exactly the same as any other RSpec test

@css[just-code](bundle exec rspec spec)
or
@css[just-code](rspec spec)

* this will run all the tests in the spec folder.
* you can also use a Rakefile to automate more complex tests


---?terminal=sessions/serverspec_samvera_demo.cast&theme=monokai&font=14px&color=#000&title=github.com/UCLALibrary/serverspec-samvera

---
# Scaling up to more than one server
https://tinyurl.com/uclalibrary-serverspec-samvera

---
# Spec_helper
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

---?image=assets/images/serverspec_components.jpg&size=contain

---
# Gotchas
* you'll need to be sure the @css[just-code](ss) command is available on the test target
  * this installed by default on RHEL
  * for Ubuntu, you'll need to install the @css[just-code](iproute2) package
* you'll need to be sure @css[just-code](/usr/sbin) is in the path, if your test target is RHEL
  * you can set the @css[just-code](:path) in spec_helper


---
# Containers? Docker?
* many options are available, worth researching
* DockerSpec: https://github.com/zuazo/dockerspec

---?terminal=sessions/docker-cantaloupe-demo.cast&theme=monokai&font=14px&color=#000&title=github.com/UCLALibrary/docker-cantaloupe

---
# Credits
* Story of the newbie (and the inspiration for this talk):
https://jjasghar.github.io/blog/2013/07/12/serverspec-the-new-best-way-to-learn-and-audit-your-infrastructure/
* _ServerSpec Components_, adapted from ["Introduction to Test-Driven Docker Development,"](https://entwickler.de/online/development/docker-test-driven-development-b-170207.html) by Peter Roßbach,  Wednesday, August 12, 2015, [Entwickler.de](https://entwickler.de/)

---
# Thanks! Questsions?

Slides:
[github.com/hardyoyo/code4lib19-serverspec-workshop](https://github.com/hardyoyo/code4lib19-serverspec-workshop)

Note:
Thanks for coming! Any questions?
