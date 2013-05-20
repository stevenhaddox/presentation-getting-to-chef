# Getting to Chef

!SLIDE

# Getting to Chef

&nbsp;

### Automate provisioning a server to run chef sans sudo

&nbsp;

#### @stevenhaddox [GitHub, App.net, Twitter]

!SLIDE

## Why Would You Do This?

* Corporate environment
* Misinformed policies
* Strict security team
* Pick your battles

!SLIDE left

## What Is Assumed

* Vagrant VM with CentOS 5.9 Minimal
* Non-sudo user on the VM
* OpenSSH installed
* Libraries pre-installed:
  * `$ perl -v; #=> 5.8.8`
  * `$ python -V; #=> 2.4.3`

!SLIDE

!["Use the Source"](images/luke.jpg)

!SLIDE left

## From the Outside In

* Chef will install as a Ruby gem
* gcc is needed for chef's native extensions
* JRuby runs Ruby & installs easily
* Java is needed for JRuby
* GNU Stow manages software cleanly

!SLIDE

## Infrastructure

```bash
$ mkdir ~/src
$ mkdir -p ~/opt/stow
$ echo "export STOW=$HOME/opt/stow" >> ~/.bashrc
$ echo "export SRC=$HOME/src" >> ~/.bashrc
$ echo "export PATH=\$HOME/opt/bin:\$PATH" >> ~/.bashrc
$ source ~/.bashrc
```

!SLIDE

## Install GNU Stow

```bash
$ cd $SRC
$ wget http://bit.ly/stow-src
$ tar -xzvf stow-2.2.0.tar.gz
$ cd stow-2.2.0
$ ./configure --prefix=$STOW/stow-2.2.0
$ make
$ make install prefix=$STOW/stow-2.2.0
$ cd $STOW
$ ./stow-2.2.0/bin/stow stow-2.2.0
$ source ~/.bashrc
```


!SLIDE

## Install the JDK

```bash
$ cd $SRC
$ wget --no-check-certificate http://bit.ly/jdk-7u12-x64 -O jdk-7u12.tar.gz
$ tar -xzvf jdk-7u12.tar.gz
$ mv jdk1.7.0_12 $STOW
$ cd $STOW; stow jdk1.7.0_12; source ~/.bashrc
$ which java
#=> ~/opt/bin/java
$ java -version
#=> java version "1.7.0_12-ea"
```

!SLIDE

## Install JRuby

```bash
$ cd $SRC
$ wget http://bit.ly/jruby-174
$ tar -xzvf jruby-bin-1.7.4.tar.gz
$ mv jruby-1.7.4 $STOW
$ cd $STOW; stow jruby-1.7.4; source ~/.bashrc
$ which jruby
#=> ~/opt/bin/jruby
$ jruby --version
#=> jruby 1.7.4 (1.9.3p392)
```

!SLIDE

## Install gcc for C Extensions

```bash
$ cd $SRC
$ wget http://bit.ly/gcc-src
$ tar -xjvf gcc-4.8.0; cd gcc-4.8.0
#TODO: Figure this out...
```

!SLIDE

## Enable C Extensions in JRuby

```bash
$ jruby -S gem --version
#=> 1.8.24
$ echo "cext.enabled=true" >> ~/.jrubyrc
```

!SLIDE

## Install chef as a gem

```bash
$ jruby -S gem install chef
#=>
Building native extensions.  This could take a while...

```

!SLIDE

!["That's a BINGO!"](images/bingo.jpg)

!SLIDE

## What Did We Accomplish?

#### You can now use Chef to automate your server environment without sudo!

* Tweak your chef scripts to:
  * Install from source, vice package manager
  * Use stow to maintain/update source installs

!SLIDE left

## Improvements

* [✓] Automate ssh access to the server from Ruby
* [✓] Automate setting up a user's pubkey for key-based SSH authentication on all accessible accounts
* TODO: Integrate the steps presented into [SUPPORT](https://github.com/stevenhaddox/SUPPORT)
* TODO: Automate this process as a simple rake task
* TODO: Create sample non-sudo chef cookbooks