# Running qcachegrind/kcachegrind on MacOS

Here are a 4 options for visualizing cachegrind files using [qcachegrind/kcachegrind](http://kcachegrind.sourceforge.net/html/Home.html)

# Option #1: brew

Do you like brew? Do you have a MacOS version that brew supports? If you answered yes to both, you are done:

```
brew install qcachegrind
```

# Option #2: Seiden Group pre-compiled

No idea what these guys are about, but they have a pre-compiled qcachegrind app that can downloaded directly:

```
wget https://repo.seidengroup.com/cachegrind/macos/amd64/QCachegrind-20201208.app.zip
```

I found this on [their website](https://www.seidengroup.com/profiling-tools-for-php-qcachegrind-download/). Hopefully they reliably host and update it. If they do not, in this repo is a .zip of the app, which works on at least 10.14.6 (18G103).

# Option #3: docker-kcachegrind

You can run kcachegrind through a Docker image thanks to [this repo](https://github.com/Quetzacoalt91/docker-kcachegrind).

### Docker

Install https://docs.docker.com/get-docker/

You can test your installation with `docker`. If you installed Docker Desktop, **you may need to open the app** before `docker` becomes exposed.

### XQuartz

Go to https://www.xquartz.org/ and download XQuartz. After a successfull installation, `XQuartz.app` should be in `/Applications/Utilities`. Further test your installation by typing `xhost` into your shell.

Now open `XQuartz.app` and in Preferences -> Security, make sure '_Allow connections from network clients_' is checked. **You only need to do this once**.

### Running kcachegrind

In your shell, open XQuartz:

```
open -a XQuartz
```

A shell window should pop up titled xterm. **This is the shell to be used for all subsequent commands**.

Store your IP address as the variable `$IP`:

```
IP=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')
```

Then:

```
xhost + $IP
```

And finally, 

```
docker run --rm -e DISPLAY=$IP:0 -v /tmp/.X11-unix:/tmp/.X11-unix -v <YOUR_FOLDER>:/cachegrinds quetzacoalt/kcachegrind
```

`<YOUR_FOLDER>` should house where you are dumping your `cachegrind.out` files. **Note** kcachegrind expects your cachegrind files to be prefixed with `cachegrind.out`, e.g. `cachegrind.out.test`.

This should open kcachegrind, and you can open up a cachegrind file in `Root/cachegrinds`.

### Issues

Unfortunately, the source code is lost so line-by-line inspection is not possible via this method.

# Option #4: Compile from source

The biggest bottleneck in compiling q/kcachegrind from [source](https://github.com/KDE/kcachegrind) is that Qt is required. But currently the only easy way to install Qt is with `brew`, in which case just use Option #1 (`brew install qcachegrind`).
