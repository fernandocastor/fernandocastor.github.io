---
layout: post
title:  "Installing Swift on Linux"
date:   2016-01-06 23:10:06
categories: general
---
Today I installed Swift on Linux. It was much more straightforward than I thought it would be. The Ray Wenderlich [website](http://www.raywenderlich.com/) has a nice [tutorial](http://www.raywenderlich.com/122189/introduction-to-open-source-swift-on-linux) on how to do that. Nevertheless, this tutorial assumes that one does not have Linux installed. Therefore, it is a bit overlong and not very interesting for me, since I'm running **Linux Mint (v.17 'Qiana' with Cinnamon 64-bit)** natively. The [Swift.org](http://www.swift.org) website also includes step-by-step [instructions](https://swift.org/download/#linux) on how to install Swift. The emphasis, in this case, is on contributors who will be sending pull requests or contributing directly to the Swift distribution. Therefore, it spends quite some space talking about PGP keys. Again, not quite my thing. 

The place where I found the definite set of instructions was Swift's [README.MD](https://github.com/apple/swift/blob/master/README.md) file. Since there may be other lazy developers out there who want to install Swift on Linux, I present below the lists of steps I followed, almost *ipsis litteris*, in terms of the commands I used on the Linux shell:

{% highlight bash %}
git clone https://github.com/apple/swift.git
sudo apt-get install git cmake ninja-build clang python uuid-dev libicu-dev icu-devtools libbsd-dev libedit-dev libxml2-dev libsqlite3-dev swig libpython-dev libncurses5-dev pkg-config
sudo apt-get install clang-3.6
sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-3.6 
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-3.6 100
cd swift
./utils/update-checkout --clone
./utils/build-script -t
{% endhighlight %}

A few thousands of compiled and linked files later, Swift was ready. It is important to emphasize that I already had ``cmake``, the build system used by Swift, installed. If that is not the case, you'll need to install it. I haven't tried that myself.


