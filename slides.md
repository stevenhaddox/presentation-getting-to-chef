# Getting to Chef

!SLIDE

# Getting to Chef

### Automate provisioning a server to run chef without sudo

#### @stevenhaddox [App.net, Twitter]

!SLIDE left

# What Is Assumed

* Vagrant VM with CentOS 5.9 Minimal
* Non-sudo user on the VM (we'll use `sysadmin`)
* OpenSSH installed

!SLIDE left

# TDD It

* Chef will install as a Ruby gem
* gcc is needed for chef's native extensions
* JRuby runs Ruby & installs easily
* Java is needed for JRuby

```bash
$ mkdir ~/src
$ mkdir ~/opt
```

!SLIDE

## Install the JDK

```bash
$ cd ~/src
$ wget --no-check-certificate http://bit.ly/jdk-7u12-x64 -O jdk-7u12.tar.gz
$ tar -xzvf jdk-7u12.tar.gz
$ mv jdk1.7.0_12 ~/opt
$ ~/opt/jdk1.7.0_12/bin/java -version
#=> java version "1.7.0_12-ea"
$ echo "export PATH=~/opt/jdk1.7.0_12/bin:$PATH" >> ~/.bashrc
$ source ~/.bashrc
$ which java
#=> ~/opt/jdk1.7.0_12/bin/java
```

!SLIDE

## Install JRuby

```bash
$ wget http://bit.ly/jruby-174
$ tar -xzvf jruby-bin-1.7.4.tar.gz
$ jruby-1.7.4/bin/jruby --version
$ mv jruby-1.7.4 ~/opt
$ echo "export PATH=~/opt/jruby-1.7.4/bin:$PATH" >> ~/.bashrc
$ source ~/.bashrc
$ which jruby
#=> ~/opt/jruby-1.7.4/bin/jruby
$ jruby --version
#=> jruby 1.7.4 (1.9.3p392)
```

!SLIDE

## Install gcc for C Extensions


!SLIDE

## Enable C Extensions in JRuby

```bash
$ jruby -S gem --version
#=> 1.8.24
$ echo "cext.enabled=true" >> ~/.jrubyrc
$ jruby -S gem install chef
#=> 
```

!SLIDE

## Install chef as a gem

```bash
$ jruby -S gem install chef
#=>
Building native extensions.  This could take a while...
# Errors without gcc currently installed
```