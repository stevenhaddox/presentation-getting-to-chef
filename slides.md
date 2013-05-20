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

## What I've Tried

* Figuring out how to install GCC via source
* JRuby install with JDK
* Installing Ruby via RVM w/ packages
* Ruby from source with:
	* libyaml
	* zlib
	* openssl


!SLIDE left

## From the Outside In

* Chef will install as a Ruby gem
* RubyGems needs Ruby
* Ruby needs:
  * gcc to compile MRI
  * OpenSSL is needed for chef's dependencies
  * zlib is needed for RubyGems and/or chef
  * libyaml is needed for RubyGems and/or chef

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

GNU Stow manages software packages cleanly

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

## Install gcc for C Extensions

Due to a lack of time, ask your sysadmin to run:

```bash
$ sudo yum install gcc
$ gcc --version
#=> gcc (GCC) 4.1.2
```

I'll update this presentation with how to install  
gcc from source once I've actually done it.

!SLIDE

## Install Ruby 1.9.3

```bash
$ \curl -L https://get.rvm.io | bash -s stable --autolibs=disabled
$ rvm --version
#=> 1.20.10
$ rvm pkg install libyaml
$ rvm pkg install zlib --verify-downloads 1
$ rvm pkg install openssl
$ rvm install 1.9.3 --with-opt-dir=$rvm_path/usr
$ rvm use 1.9.3 --default
$ ruby -v
#=> ruby 1.9.3p429
```

!SLIDE

## Install chef as a gem

```bash
$ gem install chef --no-ri --no-rdoc
#=> Building native extensions.  This could take a while...
#=> 17 gems installed
$ which chef-solo
#=> ~/.rvm/gems/ruby-1.9.3-p429/bin/chef-solo
$ chef-solo --version
#=> Chef: 11.4.4
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
* TODO: Find alternative to deprecated `rvm pkg`

!SLIDE

## Things I've Tried that Failed

### (for now...)

!SLIDE left

## Installing JRuby to run Chef

* JRuby and the JDK installed easily
* JRuby C extension mode was easy to turn on
* Chef failed to install due to C extension support  
being removed from JRuby a few releases ago apparently.

!SLIDE

## Install Ruby & Dependencies

### (from source...)

!SLIDE 

## Install libyaml for Ruby

This works:

```bash
$ cd $SRC
$ wget http://bit.ly/libyaml-src
$ tar -xzvf yaml-0.1.4.tar.gz
$ cd yaml-0.1.4
$ ./configure --prefix=$STOW/libyaml-0.1.4
$ make
$ make install prefix=$STOW/libyaml-0.1.4
$ cd $STOW; stow libyaml-0.1.4; . ~/.bashrc
```

!SLIDE 

## Install zlib for Ruby

This works:

```bash
$ cd $SRC
$ wget http://bit.ly/zlib-src
$ tar -xzvf zlib-1.2.8.tar.gz
$ cd zlib-1.2.8
$ ./configure --prefix=$STOW/zlib-1.2.8
$ make
$ make install prefix=$STOW/zlib-1.2.8
$ cd $STOW; stow zlib-1.2.8; . ~/.bashrc
```

!SLIDE 

## Install OpenSSL for Ruby

This works:

```bash
$ cd $SRC
$ wget http://bit.ly/openssl-101c
$ tar -xzvf openssl-1.0.1c.tar.gz
$ cd openssl-1.0.1c
$ ./config --prefix=$STOW/openssl-1.0.1c
$ make
$ make install prefix=$STOW/openssl-1.0.1c
$ cd $STOW; stow openssl-1.0.1c; . ~/.bashrc
$ which openssl
#=> ~/opt/bin/openssl
$ openssl version
#=> OpenSSL 1.0.1c
```

!SLIDE

## Install Ruby 1.9.3 (source)

## This blows up:

```bash
$ wget http://bit.ly/ruby-193p429
$ tar -xzvf ruby-1.9.3-p429.tar.gz
$ cd ruby-1.9.3-p429
$ ./configure --prefix=$STOW/ruby-1.9.3-p429 --with-opt-dir=$STOW/../
$ make #<-- goes kaboom
$ make install prefix=$STOW/ruby-1.9.3-p429
```