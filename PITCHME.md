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
* 1/2 hour: Types of Tests
* 1/2 hour: Additional Concepts
* 1 hour: hands on with ServerSpec in Vagrant
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

---?image=assets/images/_absolutely_free_photos_original_photos_connection-of-ideas-3600x2542_26127.jpg&size=auto

Note:
So, here's a story I read a while back, maybe it'll sound familiar? You come in at a new job, you have the initial “Hey newbie, here’s your new network. You have web machines, databases, app servers, caching servers, configuration management and other boxes. We have process monitoring ...em... nagios... heh... and yes we have configuration management where you have to do code reviews to get anything out to production.” As a newbie I’m excited *this is great* but here comes the kicker. Your  trainer continues, “Ok, lets walk through each machine. There are 10 web servers, 9 of them are called www1 through www9, and one called paco.” You quickly grab a piece of paper, “Paco?”, “Yes the admin before you didn’t like standard names, he wanted to give machines ‘personality’, and we had a change after him, so we have that one off.”

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
It's this last bullet that I want to focus on for this workshop. TDD is cool and we will do a bit of it today, but I will try to keep the focus on helping you figure out what you have, and what this tool can do.

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
# TDD Time!
* today we will use a Vagrant image for a victim
* we will use the vagrant-spec plugin for Vagrant



---
# Thanks! Questsions?

Slides: [github.com/hardyoyo/code4lib19-serverspec-workshop](https://github.com/hardyoyo/code4lib19-serverspec-workshop)
Credits:

Story of the newbie:
Note:
Thanks for coming! Any questions?
