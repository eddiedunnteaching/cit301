## Introduction and motivation


The way these AWS Vocereum lab environments are setup is a curse in a lot of ways.

1. Although you can continue a lab session indefinately by clicking the `:arrow_forward: Start Lab` button again to reset the time to `1:30`. You must remember to do this. I suggest setting a timer for an hour and 15 minutes or something so you are not right up against the time and clicking the button, repeat.

2. It won't (or at least I have not figured out how) to make a `user` in any of these labs so that you can make a programmatic access key that you can then use for any sort of automation of the actual AWS infrastructure like you can do with tools like: `awscli`, `Cloud Formation`, `Terraform`, etc. (Heck even `Ansible`)

I thought originally that the cloud automation tools like `Terrafom` and `Cloud Formation`. would be a good place to try and take this course. We could still do that depending on how this Ansible stuff goes but you guys would have to put your credit card on the line.  I would try and tell you how to do everything on the free tier and not get charged. It would be up to you though ultimately to manage that cost as I would have no control. 

I would rather not go down that path with your guys so instead I thought I would show you a tool that is widely used and can probably help you no matter where you end up (I hope). 

This ephemeral environment we have with Voceruem is great because there is no way to get charged. It will just stop working when you hit $150 I think for each lab which is impossible to do with what I will be showing you becuase it tears everything down automatically when you stop working. 

It is bad because you have to repeat the steps by clicking in the console to create resources everytime you launch the labs. I am going to show you a way to be able to quickly build and test things for your projects. You will still have to re-create the resources and generate a new `ssh` key each time but hopefully that should take just a minute or two and then as long as you keep clicking that :arrow_forward: `Start Lab` button before the `1:30` is up you can keep working for as long as you need. 

I think this is actually a blessing as it forces you to repeat the steps enough to really drive them home and also forces you to do the automation so that you can `save` your work. Don't worry if that seems fuzzy it will make more sense.

This conversation about cost concerns individuals as well as organizations and is something you should always be questioning how you can do better.

A little bit of research and willingness to do things yourself can pay rewards MANY times over in cost savings. 

## Cost

Before we even get started since you guys have been drinking the AWS Kool Aid and this is supposed to be a vendor neutral class... Here ya go.


***Managed Services Are Expensive***

***VERY expensive***


You can oftan save many times over in cost for whatever it is you are doing if you actually spin up the cloud resources yourself. Use both cloud and machine automation tools that leverage configuration files stored in source control so that you can roll back. Have a change control process even if it is informal. Key players on your engineering team need to know what is going on and have the ultimate opportunity to say `go` or `no go`. 

As an example `Amazon RDS` is great and can give you peace of mind but you can also do replication, fail-over and fault tolerance and backups with the tools that ship with things like `postgres` or `mariadb` at a small fraction of the cost.

Using cheaper providers if you do not need the features of AWS.   If all you need are need VM's in the cloud there are many other providers that can give you the same resources for much more reasonable price than AWS. Digital Ocean for instance offers managed database services that are similar to RDS but much cheaper than AWS. 

If you know you have dedicated and consistent need for on-site on on-WAN resources for apps that will only be used in-house and only need to handle a set amount of traffic or maybe to service development teams. In these cases you can still get much of the benefit of the cloud environment in an on-premise environment with a product like [Open Stack](https://www.openstack.org/).

[Open Stack](https://www.openstack.org/) allows your teams the ability to provision resources on-demand  much like a cloud provider but have the associated costs come under a more traditional `Cap-Ex` model. This model is much cheaper if you know you will need the dedicated capacity. Cloud computing is someone else's computers and you are paying a pretty penny to use them.

## Another piece of perspective

Humans are not the best at doing tasks perfectly. Even the most graceful among use  spill drinks, burn our hands, etc from time to time. This is not limited to the physical realm. Even the brightest and most sharp among us from time to time make digital flubs. Even with the tasks we have done so far in this course it is easy to get confused to what you are doing switching back and forth between the console and the directions. Even if you use split screen. I am not claiming to be the brightest or sharpest but know I have had to go back a redo at least two of the labs because I missed something and I have been doing this a for a fair minute.

The point I am trying to drive home is that clicking around in the AWS console (or even manually running commands at the command line ) is a tedious and error-prone process. I love us as a species but we are not the best automation machines. We have thoughts that wander and people that demand our attention and all these other things that can take our focus away from what we are doing or rather *supposed* to be doing in the present moment. Especially when you are dealing with critical infrastructure in the cloud one falso move could have disasterous consequences. I have found two tactics to fight this tendency. 
 
1. Automate as much as possible. Automate things that you do often first but automate things you don't do often just to practice automating. This both makes you more proficient at the skill and also allows your team mates to benefit as well by learning from what you have figured out even if you don't say a word to them.

2. When building automation tools always think of what can go wrong. You want to have confidence that your tools will at the very least not break anything if they fail. This only comes with practice and startign small. 

In `Ansible` point 2. is accomplished mainly through striving to make everything `idempotent`. All that means is that the tool *only* makes changes that need to be made and otherwise leaves things alone. This also means you can run it multiple times and if everything is done it will make no changes. IE no chance of screwing anything up.



## [Ansible](https://docs.ansible.com/)

It is the automation tool [`Ansible`](https://docs.ansible.com/). [`Ansible`](https://docs.ansible.com/) is an open-source automation tool that is simple to use and very powerful. Although it is maintained by and has a paid-support product through `Red Hat` I want to stress that it is a free and open-source product at its core. 

The  name [`Ansible`](https://docs.ansible.com/) comes from Sci Fi. It is first said to have first been mentioned by [Ursula K. Le Guin](https://en.wikipedia.org/wiki/Ursula_K._Le_Guin) and then later used widely in the genre and has come to refer to a device capable of communicating instanteanously across great interstellar distances. So the idea is that you can do that same thing with all your servers. Whether those servers are virtual or physical. Or if they are single user workstations instead. Or  even the machine you are sitting in front of!

In fact [`Ansible`](https://docs.ansible.com/) has thousands of libraries to help with all kinds of automation tasks.

- https://docs.ansible.com/ansible/latest/collections/index.html
- https://galaxy.ansible.com/

You will find everything from Linux, Windows, MacOS, Cloud Services, a dizzying array of things. 

One the best features of [`Ansible`](https://docs.ansible.com/) is that it is agent-less. This means that you do not need to install any special software or run any special services running on any additional ports. You can do the automation over the same channels you already use for management. In our case here for this lab the communication channel is `SSH`.


Many servers in small IT shops are what is referred to as `Snowflake Servers`. This means literally that in order to replace that resource there would be multiple manual commands, etc that would be needed to get that resource back up or if you need to add capacity rapidly build new servers (or AMI images, docker containres, etc) to meet an increase in load demand.

[`Ansible`](https://docs.ansible.com/) does not require doing any python coding or have any special syntax outside of being [YAML](https://yaml.org/)-based. 


## Data file formats 

We have so far seen two different file formats that are widely used I want to call out specifically.

- [JSON](https://www.json.org)
- [YAML](https://yaml.org/)

If you continue in the cloud domain:  Both of these formats you will see come up again and again. They are not hard but both have a syntax and can be frustrating.

AWS historically tends towards `JSON` although in many cases these days I think you can use either with configuration chagnes if you have a strong preference.

Ansible makes extensive use of `YAML`. Here is a great introduction from the [ansible docs](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html) 

Okay, let's catch our breath. We are starting to get ahead of ourselves.

First we need to install `Ansible` on our machines:


## Ansible and Windows

If you are a Mac or Linux user skip to the next section. If you are Windows... continue on.

We are going to use something from Microsoft called `Windows Subsystem for Linux`. This is a way to have a more native shell experience in windows. It is also required to run `Ansible`. First the docs:

[Install WSL] (https://learn.microsoft.com/en-us/windows/wsl/install)

![Install WSL Video Demonstration](./vids/WSL_Setup_Video.mkv) 


Now that part is out of the way let's actually install `Ansible` in our WSL installation. Luckily as always the `Ansible` docs are our friend.

[Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)


![Install Ansible Video Demonstration](./vids/Ansible_Install_WSL.mkv)

![Windows Important Point - Finding Files](./vids/WSL_WHERE_ARE_MY_FILES.mkv)

??

## Ansible and Mac (and Linux)


While we are on the subject of Python, Mac and Linux. Mac as well as all Linux distributions I am aware of use Python as part of the operating system so this means there is already a version of python living on your system. You can certainly install packages via `pip` into the system Python installation but I don't recommend it. Have a look at [PyEnv](https://github.com/pyenv/pyenv). [^1]

In the case of Mac though I would suggest using `Homebrew`. If you do not already use [`Homebrew`](https://brew.sh/). It is the best. Install it and then run:

```
brew install ansible
```

That's It!

Now that we have `Ansible` installed on just about any OS we might have around let's proceed to the bext step:

How to connect to our EC2 instances using `SSH`?

## AWS

Since AWS does not allow password authentication. Before we launch our instances we first need to create a key pair to use for access.


Let's start by launching our [EC2 lab](https://awsacademy.instructure.com/courses/4354/modules/items/686600) from before.

![relogin](./images/relogin.png)

If you ever see anything like the above you only need to click logout close that tab and then relaunch the console from the `AWS :green_circle:` link. You do NOT need to restart the lab.


The first thing you want to do when you first start Vocereum is to create a `key pair` that you can use to access the instances. Unfortunately you will have to do this each time you start a new session. You can ease the pain of this by saving the `.pem` file to the same location and with the same name each time. (The old one will no longer be good for anything)

The are a couple of ways to get to this section. One is by clicking on the navigating to the `EC2` dashboard

![EC2 Dashboard](./images/ec2_dash.png)

Click on the `Key pairs` link and you will be taken here:

![key pairs](./images/key_pairs.png)

Then click on `Create key pair`.

![create kp](./images/create_kp.png)

You want to give it a name (I chose `kp`) and then select `ED25519` and `.pem` and click `Create key pair`.

![success](./images/successfully_created.png)

Now you need to create the instance.

- Choose a name
- Select the default `Ubuntu` AMI (22.04)
- Select the Key Pair you made 
- Create New security group that allows `SSH` traffic from anywhere

Click `Launch Instance`

Once the launch completes you will get a `Success` message.

![instance summary](./images/instance_summary.png)

Click on the `instance id` link and on the instance summary page click the link to copy the public IP to your clipboard.

Ansible can manage Windows hosts but the management hosts must have a more Unix-like enviroment to work properly. If   `Ansible` is written in `Python` so  if you are on a Mac or Linux it can be installed perhaps most easily via `pip`. I will leave that as an exercise for the reader. These steps should work for us.


## Connecting to the AWS instances via SSH.

OK now... connecting to this instance with `SSH`

If you followed along so far this command should get you in

```bash
ssh -i <path to your kp.pem> ubuntu@<public IP of instance>
```

I will ask you if you want to save the key in your local keystore. Say yes.

![ssh add key](./images/ssh_add_key.png)

![success login](./images/success_login.png)


You will likely get an error message about permisions. 


![unprotected file](./images/unprotected_file.png)


**Windows User Gotcha: Please first copy the `.pem` file from the place you downloaded it to your wsl home directory `cp /mnt/c/Users/<windows user>/Downloads/kp.pem /home/<ubuntu user>/kp.pem`
Use the following command on your `.pem` file.**


```bash
cd
chmod 600 kp.pem 
```

This will make only the `user owner` able to see the contents of the private key.


## Take a screen shot and submit. This is the SSH Assignment


If you get stuck:

![WSL Where are my files](./vids/WSL_File_Location.mkv)


`Ansible`'s lowest unit of code is called a [`module`](https://docs.ansible.com/ansible/latest/user_guide/modules_intro.html). 

## Ansible Ad-Hoc

The simplest way to run `ansible` in fact runs a module by itself and is called [`ad-hoc`](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html). As the name implies this is a method that lets you run ansible modules in a one-off manner.

Before you connect to the EC2 instance via ansible we must first make sure that the `host` key has been saved in our `~/.ssh/known_hosts` file. We can do this via `ssh`. 
 
If you get an ansible error about `Host key verification failed.` that is what is going on.

Let's try out a simple ping to our EC2 instance.

```bash
ansible all --key-file=kp.pem -u ubuntu -i <public IP of instance>, -m ping
 ````

![ansible ping](./images/ansible_ping.png)


Let's break down the command:

- `all` means to imply all hosts in the inventory. In this case there is only one host in the inventory so it is really just one host.
- `key-file` is the path to our `.pem` file we downloaded when we made our key pair.
- `-u ubuntu` specifies that we want to connect to the instance as the user `ubuntu`. This is something that you would just have to know or read. It is an AWS ubuntu AMI thing.
- `-i 44.203.84.162,` Is the inventory we are working with. This is usually a YAML or even `ansible` `INI` format. **Please note that the trailing comma is required or it will not parse it as a list.**
- `-m ping` Last but certainly not least tells which module we are going to run. The ping module works much like the ping command in networking. It checks to see that the host is avaialable and you can connect and manage.

Ok great. Now we know how to manage to our instances with ansible! We are on our way!

Another thing we can do to simplify things is to put some of these options in a configuration file so we don't have to keep typing them. `Ansible` has an `ansible.cfg` file we can put in our project directory that serves this purpose. 

Here are the contents of `ansible.cfg`

```ini
[defaults]

private_key_file = kp.pem
remote_user = ubuntu
#inventory = inventory.yml

```

Now we can run our `ad-hoc` command with fewer options:

```bash
 ansible all -i <public IP of instance>, -m ping
 ```

 In a real scenario we would be running these commands on many servers at once specified by the keyword `all` in an inventory file referenced in  `ansible.cfg` as shown in the commented line above. We could then even further simplify our command incantation to something like.

```bash
 ansible all  -m ping
 ```

### Let's do something useful.

Let's do another real example with our server. Let's install an apache web server using the apt module.

```bash
ansible all -i <public IP of instance>, -m ansible.builtin.apt -a "name=apache2 state=present" --become
```

Notice:

1. We do not have to know anything about how to run the actual command(s) to install the software on the server. We only need to know the name of the package.
2. This command is `idempotent`. It will only install `apache2` if it is not already present.
3. We used a new option `--become` this tell `ansible` that we will need to elevate permissions (run `sudo`, ie need admin access) to run this command.


If all goes well you should now have a running web server with the default page. Note you will need to modify the AWS security group to allow web traffic like you did in the actual lab instructions.

![apache default](./images/apache_default.png)


You can also do things like copy files to remote systems. Again like in the actual lab instructions let's create a simple `Hello World` html file in our project directory on our local computer.

`index.html`
```html
<html><h1>Hello World!</h1></html>
```

```bash
ansible all -i <public IP of instance>, -m ansible.builtin.copy -a "src=index.html dest=/var/www/html/index.html" --become
```

The above will replace the default apache page with our `Hello World`.


Outside of the package updates (which we will see whortly)  we have done the equivalent to our EC2 instance that was done to the Amazon Linux instances in the lab.

What we have done is better for a couple of reasons:

1. Unlike that `data` blob that just runs once and we have no way of knowing if it worked. Ansible will tell you if it could or could  not accomplish a task and give you an error message as to why so that you can troubleshoot.
2. Since the `data` block only runs once if some knucklehead comes along and removes `apache2` then we would have to restart the entire instance. Since this is `idempotent` it will only install apache if it is missing so no unneccesary changes are made.

In fact the more complex your builds are the more troublesome a solution like the one shown in the lab becomes. I have heard and like to talk about the idea of `snowflake` servers. `Snowflake` servers are the ones that require lots of attention. This is usually because everyone knows how much of a time drain it would be to rebuild if something were to happen. The data block is better than no automation I suppose but instead we want to have `cattle` servers that are similar and easily commissioned and decommisioned, configured in a simlar way, etc. This is the kind of environment `Ansible` really shines.

### shell module

You can also use the [`shell`](https://www.middlewareinventory.com/blog/ansible-shell-examples/) module to run commands like you would be running them on the remote system. Complete with input/output redirection.

In fact many companies who get started using `Ansible` will use the `shell` module and it's `|` feature to just paste their existing shell scripts into their ansible `playbooks`. [Ansible Docs shell_module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html)

As a quick example let's check out how much free memory we have on our servers:

```bash
ansible all -i <public IP of instance>, -m ansible.builtin.shell -a "free -h"
```

![ansible free](./images/ansible_free.png)


**Note: If you were doing things to lots and lots of servers at once Ansible will work through the list of hosts sustainably by only having five by default hosts executing at a given time. You can configure this to be smaller or greater.**


## Ansible Playbooks


`Ansible` has a concept called a [`playbook`](https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook). A `playbook`  is a `blueprint` for an automation task. It contains a list of `tasks` which are really just calls to `modules`. 

There is an example playbook [here](https://raw.githubusercontent.com/eddiedunnteaching/cit301/main/apt_update_w_reboot.yml).

Download this file to the same directory you have been working.

Open the file in a text editor and take a look at the steps. Can you tell what is going on? One of the things I like about Ansible it is (usually haha) clear what is going on.

![update server](./images/update_server.png)

At first glance and as previously mentioned the file is written in YAML. Yay. Let's break down each part.

The very first line of the file should have an otherwise empty line with three hyphens `---`.

Next we have some configuration options that will apply to the rest of the elements in the `playbook`.

- `hosts: all` Means that this playbook will run against all hosts in the inventory by default.
- `become: yes` Means that `ansible` will elevate priviledges by running things with `sudo`.
- `gather_facts: no` Means that `ansible` will not run it's normal process of figuring out different things about the remote server that you can use. If you do not need it it can speed things up a lot.

The next section has the actual `tasks` that ansible will perform.

1. The first step uses the `apt` module to update the list of available packages and versions from the apt repositories.
2. Next step, again with the `apt` module we are actually updating the packages that need updating on our server.
3. Checks to see if the updates made any changes that require a reboot to take effect.
4. If a reboot is needed, perform the reboot.


As you can see each task has an associated name. If used this makes it really easy to see what is going on when your playbooks are running.

Now let's run our playubook but before we do that we need to learn the command for running playbooks. The command is un-ironically `ansible-playbook`.

`ansible-playbook` takes many of the same options as the ad-hoc `ansible` command but instead of specifying a `module` and the servers you are targeting (ie `all` in our examples), you specify the YAML file instead that contains that information. Let's see an example with the `playbook` we just looked at.

```bash
ansible-playbook -i <public IP of instance>, apt_update_w_reboot.yml
```

![playbook run](./images/playbook_run.png)

Awesome!

As you probably noticed this image had an update that required a reboot.


## Your Task

Create a playbook that does the same tasks as the `data` blob from the original lab.

You should have:

1. Installed some kind of web server (we have demonstrated apache)
2. Place some kind of `Hello World`-y web page as the default page. 
3. Update all packages and reboot if required.

### Submit your YAML file for the assignment submision in Canvas.

Some things to consider:

The lab uses Amazon's version of linux which is Red Hat based so the commands to install software are different.  

If you recall from your linux class. All linux distributions have package managers that are used to update your system, install additional software, manage dependencies, etc. `yum` is the one that Red Hat uses (well technically `dnf`) and `apt` is the one that Ubuntu uses.


Two pieces of information that are helpful:

1. There is an ansible module for `apt` that will let you install software
2. The package is not called `httpd` in `apt` it is called `apache2`


https://t-systems-mms.github.io/ansible-bad-and-good-practices/

One of the best things about Ansible is that it lets us stand on the shoulders of our digital benefactors and make use of their work in a sane and repeatable way. The Zen of Automation. This is a perfect example. 

## Final Project

Your final group project is to write an Ansible playbook that installs some kind of software (approved by me) with Ansible. 

Examples could include:

Owncloud
NextCloud
Mattermost
Seafile
Piwigo
iFolder
Element
Emby
Zimbra
Wordpress
Plex
Jellyfin
Duplicati

I have put together two working examples of what I am looking for. 

https://github.com/eddiedunn/mineos-ansible


[^1]: [PyEnv](https://github.com/pyenv/pyenv) will let you create multiple installations of Python with different versions and packages. Using PyEnv will save you hours of trouble with Python. Pro Tip use a `.python_version` file with just one line that is the name of the python version you want to use and  so you will automatically change into the correct version when you `cd`.





