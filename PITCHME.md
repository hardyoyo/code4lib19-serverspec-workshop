# Wait, what? Getting your bearings with ServerSpec
## Preconference Workshop, Code4Lib 2019, San Jose, CA

Hardy Pottinger

Digital Library Software Developer, UCLA Library

@fa[twitter] @hardy.pottinger

@fa[envelope] hpottinger@library.ucla.edu

Note:
Hi, welcome, thanks for coming.

---
# Agenda
* 1/2 hour: Intro to ServerSpec
* 2 hours: hands on with ServerSpec
* 1/2 hour: questions and discussion

Note:
More speaker notes here.

---
# We are constantly learning more about our environment
* Developers shop jobs a lot
* Have you seen the mailing lists?
* We change jobs a lot
* Our jobs change a lot all on their own
* We are always the newbie

Note:
More spearker notes here.

---
# Why write ServerSpec tests?
* Tests are documentation
* Your work group may or may not survive
* Provisioning tools come and go
* No matter what happens to the tools or your workforce, these tests will persist as documentation of your intentions and proof that the service is configured as you expected

Note:
More spearker notes here.

---
# ServerSpec
* Extension of RSpec
* Yep, it’s a Ruby gem
* Is a great way to force yourself to think about your intentions before you provision a new service
* Is a great way to get to know your existing services

Note:
It's this last bullet that I want to focus on for this workshop. TDD is cool and all... but, today, let's focus on just figuring out what we have, and what this tool can do.

---
# Installing ServerSpec
* It’s a Ruby gem, you’ll need Ruby 2.0.x+ installed
sudo gem install serverspec
* You will need Rake installed, too:
sudo gem install rake
* You will need SSH access to the servers you want to check
* You will not need Sudo on these servers, though Sudo does make things slightly easier

Note:
Sudo: I will point out areas where things may get a little dicey if your test logs in as a normal user. It all works, but you have to adjust some tests for unprivileged users.

---
# Vagrant to the rescue!
* today we will use a Vagrant image for a victim
* we will use the vagrant-spec plugin for Vagrant

---?image=assets/images/GermanSubmarineControlRoom1918.jpg&size=auto

Note:
More speaker notes here. This is an example of an image-only slide.

---
# Thanks! Questsions?

Slides: [github.com/hardyoyo/code4lib19-serverspec-workshop](https://github.com/hardyoyo/code4lib19-serverspec-workshop)
Image Credits:

Note:
Thanks for coming! Any questions?
