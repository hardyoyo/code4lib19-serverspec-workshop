# Wait, what? Getting your bearings with ServerSpec

Hardy Pottinger

Digital Library Software Developer, UCLA Library

@fa[twitter] @hardy.pottinger

@fa[envelope] hpottinger@library.ucla.edu

Note:
Hi. I'm Hardy Pottinger, I'm a developer at UCLA Library. This talk is about ServerSpec.

Here's a thing I know...

---
# We are constantly learning about our environment
* Developers shop jobs a lot
* Have you seen the mailing list?
* We change jobs a lot
* Our jobs change a lot all on their own
* @css[just-code](We are always the newbie)

Note:
We are constantly learning. Heh, mailing list. And our jobs change all the time. We are always the newbie.

---
# Why write tests?
## With ServerSpec or any similar tool?
* Tests are documentation
* Your team may not survive
* Tools come and go
* No matter what happens to the tools or your team, tests will persist as documentation of your intentions and proof that the service is configured as you expected

Note:
How can tests possibly help? And why ServerSpec?
Well, honestly, it's one option. Tests are documentation. Your team may not
survive. Tools come and go. But if you write tests in ServerSpec, your tests will
remain accessible to anyone reading the code. I'll show you.

---
# ServerSpec
* Extension of RSpec
* Is a Ruby gem

Note:
ServerSpec is an extension of RSpec, which is a testing framework
for Ruby. Before we get too much further...

---
# Software Reuse
## spec spec spec...
* RSpec
* ServerSpec
* DockerSpec
* SpecInfra
* ¯\\_(ツ)_/¯
Note:
...you'll hear me mention a few different words with "spec" in them. There will
be a diagram, but it's too much detail right now. This is normal Ruby stuff.
Software reuse.

---
# Installing ServerSpec
* It's a Ruby gem, you'll need Ruby 2.0.x+ installed
@css[just-code](sudo gem install serverspec)
* Rake, too:
@css[just-code](sudo gem install rake)
* SSH access to the servers you are testing
* Sudo not required, but it makes life easier

Note:
Yep, it's Ruby, and you'll need Rake. Sudo helps, but it's not required.

It does have an init command, which can get you started.

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

Note:
So, you type in serverspec-init and answer a few questions, pick your OS, select
a backend type, blah blah bla. And boom, you get a nice set of files, a basic
starter kit. Let's look at the sample_spec file.

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
You've seen Rails, you've seen a "helper", it's a way to pull out some
complexity to improve the readability of your code. For ServerSpec, it helps
improve the readability of your tests. Skip it for now, instead, let's look at
all the other pieces we get. Each of these other sections are what are called
Resources. In the sample, we have a package resource, a service resource, a
port resource and a file resource. And the tests are pretty clear about what
they expect, right? It should be a file, it should contain something.
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

Note:
Of course there are many more resources. Every time I look at this page I notice
something I've never used before, or want to try again.

OK, so, that's what the tests look like, what about running them?
---
# Run the tests
* RSpec tests usually go in a folder called @css[just-code](spec)
* calling ServerSpec is exactly the same as any other RSpec test

@css[just-code](rspec spec)

* this will run all the tests in the spec folder
* or @css[just-code](spec/ask/for/whichever/spec.rb) you want
* you can also use a Rakefile to automate more complex tests

Note:
Normal RSpec practice puts the tests in a spec folder. You run them with the
rspec command. Give it a path to a specific file, just that file will be run.
Or a folder, all the tests in the folder will run. Or you can get fancy with a
Rakefile. Here, I'll show you a demo.
---?terminal=sessions/serverspec_samvera_demo.cast&theme=monokai&font=14px&color=#000&title=github.com/UCLALibrary/serverspec-samvera

---
# Scaling up to more than one server
https://tinyurl.com/uclalibrary-serverspec-samvera

Note:
Here's a link to the tests that I just ran in that demo. I used a Spec_helper
to add the ability to include shared libraries of test code. But let's just peek
at the code.

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
your tests. I'll skip the details, there are links in the slides. The other
piece of this is a Rakefile.

---
# Use a Rakefile
* automate the running of complex tests
* Rake is a software task management and build automation tool
* Similar to Make
* Written in Ruby

Note:
You know, it's kinda like Make, it lets you put together smaller pieces
into a whole.

---
# sharing code
https://serverspec.org/advanced_tips.html
https://tinyurl.com/uclalibrary-serverspec-samvera

Note:
Here's the code for that demo. And a link to the documentation that inspired it.
I promised a diagram of all this stuff, here's one I found.

---?image=assets/images/serverspec_components.jpg&size=contain

Note:
It's all an extension of RSpec. ServerSpec sits on top of another gem called
SpecInfra. SpecInfra handles all the specific calls to whatever kind of thing
you are testing. This is important if you find a bug.

---
# Gotchas
* you'll need to be sure the @css[just-code](ss) command is available on the test target
  * this installed by default on RHEL
  * for Ubuntu, you'll need to install the @css[just-code](iproute2) package
* you'll need to be sure @css[just-code](/usr/sbin) is in the path, if your test target is RHEL
  * you can set the @css[just-code](:path) in spec_helper

Note:
Some gotchas I know about. You'll need the ss command, it's like netstat but not
deprecated. It's included on Redhat, you'll need to install iproute2 on Ubuntu.
And sbin needs to be in your path, Ubuntu does that, but RH doesn't. You can set
the path in the spec_helper. The big one is that most of these tools
are run by a single developer. And he has issues turned off in GitHub. So, it'll
be up to you to at least suggest a fix for any bug you find, and make a pull
request.

---
# Containers? Docker?
* many options are available, worth researching
* DockerSpec: https://github.com/zuazo/dockerspec

Note:
What about containers? Docker? There are options, and fun reading. I recommend
DockerSpec, here, let me show you a demo.

---?terminal=sessions/docker-cantaloupe-demo.cast&theme=monokai&font=14px&color=#000&title=github.com/UCLALibrary/docker-cantaloupe

---
# Credits/Links/Thanks
* Inspiration for this talk: JJ Asghar's /Rants and Ramblings blog post on ServerSpec:
https://jjasghar.github.io/blog/2013/07/12/serverspec-the-new-best-way-to-learn-and-audit-your-infrastructure/
* _ServerSpec Components_, adapted from ["Introduction to Test-Driven Docker Development,"](https://entwickler.de/online/development/docker-test-driven-development-b-170207.html) by Peter Roßbach,  Wednesday, August 12, 2015, [Entwickler.de](https://entwickler.de/)

Slides:
[github.com/hardyoyo/code4lib19-serverspec-workshop](https://github.com/hardyoyo/code4lib19-serverspec-workshop)

Note:
Credits, links in the slides. Thanks!
