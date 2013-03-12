# Draft in progress - Do not use


# How to install Ruby locally from source

This how-to is an approach to use to solve the following problems:

- Ruby version management tools (RVM, rbenv, etc) add complexity when full features are not required.
- Ruby versions installed by package managers (apt-get, etc) lag behind the latest stable version.

The following steps detail an approach to use on a Debian derived distributions (Linux Mint, Ubuntu, etc).


**All commands are performed from the user's home directory unless otherwise stated.**

## Build tools and libraries

Install the necessary tools and libraries by running the following command:

~~~ sh
$ sudo apt-get install build-essential autoconf git libreadline-dev libssl-dev
~~~

## Ruby to build Ruby

Building Ruby from source requires an installed Ruby version. Install the package manager's Ruby version:

~~~ sh
$ sudo apt-get install ruby
~~~

## Create installation directory and add to PATH

The popular approach is to install under a `bin` directory in the user's home directory (e.g. `/home/yerv000/bin`). This approach is not recommended as it does not offer seperation of space which comes very useful when switching to a different version of Ruby.

The following approach is more flexible and adds seperation of space.

Create a local directory to hold all locally installed programs (Ruby, Node, etc):

~~~ sh
$ mkdir ~/local
~~~

Create a `ruby` subdirectory to hold the Ruby installation:

~~~ sh
$ mkdir ~/local/ruby
~~~

Add the Ruby binary to the search path. Edit the `.profile` file present in the user's home directory and append the following line:

~~~
PATH=$HOME/local/ruby/bin:$PATH
~~~

Log out and log back in for the changes to take effect.


## Create source directory and clone repository

Create a directory to hold source codes:

~~~ sh
mkdir ~/src
~~~

Clone the Ruby repository under the `src` directory:

~~~ sh
$ cd src
$ git clone https://github.com/ruby/ruby.git
~~~

## Create shell script to build and install Ruby

Create a file called `build_ruby.sh` in the `~/src` directory using the following commands:

~~~ sh
$ cd ~/src
$ touch build_ruby.sh
$ chmod +x build_ruby.sh
~~~

Copy and paste the following content to `build_ruby.sh` using your preferred editor:

```sh
cd ruby
autoconf
./configure --prefix=$HOME/local/ruby --enable-shared
make
make install
```

## List available tags and checkout version to build

Change current directory to Ruby source:

~~~ sh
$ cd ~/src/ruby
~~~

List available tags:

~~~ sh
$ git tag
~~~

Tags are version numbers and patch levels. Pick a tag and check it out. Let us pick *Ruby 2.0.0*:

~~~ sh
$ git checkout v2_0_0_0
~~~

Git should state that it is in detached head and list the tag name.


## Run build script

Change current directory to `~/src`:

~~~ sh
$ cd ~/src
~~~

Run build script:

~~~ sh
$ ./build_ruby.sh
~~~

## Verify Ruby installation

Run the following command:

~~~ sh
$ which ruby
~~~

Output should be under the user's home directory `~/local/ruby/bin/ruby`.

Verify installed Ruby version:

~~~ sh
$ ruby --version
~~~

Output should correspond to the tag version picked, in our example:

~~~
ruby 2.0.0p0 (2013-02-24 revision 39473) [x86_64-linux]
~~~

## Deleting Ruby installation

To completely delete the locally installed Ruby version execute the following:

~~~ sh
$ cd ~/local/ruby
$ rm -rf *
~~~

Now you are able to pick a different version and do a clean install.


## Maintaining different Ruby versions

Simply rename the ~/local/ruby directory:

~~~ sh
$ mv ~/local/ruby ~/local/ruby_2_0_0_0
~~~

Recreate an empty local Ruby directory:

~~~ sh
$ mkdir ~/local/ruby
~~~

Now you are ready to checkout a specific tag (e.g. *v1_9_3_392*) in the `~/src/ruby` directory and rerun the build script.

To return to the *2.0.0p0* version without deleting the just installed *1.9.3p392* version:

~~~ sh
$ mv ~/local/ruby ~/local/ruby1_9_3_392
$ mv ~/local/ruby2_0_0_0 ~/local/ruby
~~~

## Note about gems

Use bundler to manage gems. All gems will be under the `~/local/ruby` directory, so when you rename the directory to switch to a different Ruby version the gems move with the Ruby binaries.
