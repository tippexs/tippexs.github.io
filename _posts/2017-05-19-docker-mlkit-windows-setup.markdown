---
layout: post
title:  "Docker, Moby and Linuxkit - A Windows Setup Tutorial"
date:   2017-05-19 21:05:00 +0200
comments: true
categories: docker tutorial
---

# Installing Moby project and linuxkit on Windows – DEVELOPER MACHINE

#### Pre-Required:
  Windows10 Pro with Hyper-V and 60min of your time.


Today I tried or better I installed the components are necessary to run Dockers mobyprojekt and linuxkit on windows.

Before we can start building an image we should check a couple of pre-requirements first. I will also describe how to use GitBash or PowerShell to use make commands and stuff like that. So keep reading :).

![spongebob keep reading](https://media.giphy.com/media/WoWm8YzFQJg5i/giphy.gif?response_id=591f663d3fe17e6328d17717)

#### Before you can start ... 

Before we can start you should check your system environment is ready for it. We will need a couple of additional programs / tools to work with this tutorial.
1. An Text-Editor (Notepad, Notepad++, Microsoft Code, what ever :) )
2. And cmd or bash window. You can use the default Microsoft cmd or better a powershell session for running the commands. I will use GitBash in this tutorial.<br>
Cygwin will be also okay. But its quite a huge think to install. If you have it and like it -> use it!

3. The "make"-Tool for Windows. I love the lightweight MinGW-Toolset. You can download it here: 
[Download form sourceforge](https://sourceforge.net/projects/mingw/files/Installer/)<br>
[Projekt Website](http://www.mingw.org/)

After the installation I did to additonal steps. 
1. Add the path of the MinGW to my system path.
2. Rename the WinMake.exe into make.exe

No we have a basic setup to start with this tutorial.

#### Step 1: Do you have Docker for Windows installed? 
If you have Docker for windows already installed you can go to step 2 now.

If not: Let’s install Docker for Windows first. The installation process is quite strait forward.

<span style="color: red">WARNING: Your Compute will reboot 2 – 3 times! Save everything before starting the installation.</span>


1. Download the latest Windows Installer from the Docker store.
[Download Docker for Windows](https://docs.docker.com/docker-for-windows/install/) and launch the installer.	
	
2. Click on Install

![DockerInstall1]({{ site.url }}/assets/1dockerinststart.jpg)
![DockerInstall1]({{ site.url }}/assets/1dockerinstfinish.jpg)

If your Hyper-V Feature is not enabled, yet – Don’t worry. Docker will check that first before starts operating.

![DockerInstall1]({{ site.url }}/assets/1dockerinsthypervmissing.jpg)

Simply click “OK” to install the Feature. Your Computer will restart 2-3 times.

After the reboots, you can find the docker whale in your status-bar. You can check the status by right clicking the icon -> Settings.


![DockerInstall1]({{ site.url }}/assets/1dockerinstwindow.jpg)

Congratulations – Docker is now up and running and we can start with the Golang installation.

#### Step 2: Do you have Golang installed? 

If so, go directly to Step 3.<br>

You can find the Windows-Installer here:
[Golang Windows Installer](https://golang.org/dl/)

After installing you can check the go.exe is part of your global system path variable.<br>
Just type go env in your command line tool (I will use Git Bash in my example).<br>
Your output should look like this:

![GoEnv]({{ site.url }}/assets/2goenv.jpg)

Per default your GOPATH is not set globally. Have a look here how to set you GOPATH for windows globally.<br>
[Setting you Go Path](https://github.com/golang/go/wiki/SettingGOPATH)

After this step your GOPATH should be there globally. Now we are ready to download and build our moby and linuxkit binaries for windows.<br>

For a better usage later on I added the $GOPATH/work/bin/ to my global system path as well.

#### Step 3. Dowload and install linuxkit and moby for Windows

There are several ways to install moby and linuxkit. Just have a look into the Github-Repo.
[Linuxkit @ Github](https://github.com/linuxkit/linuxkit)

You can download or clone the github repo and run make to build the binaries. Let’s have a look on that option.

Create a directory somewhere on your drive and clone the linuxkit github repo into it.
{% highlight text %}
git clone https://github.com/linuxkit/linuxkit.git
{% endhighlight %}

Now, we can start making the binaries.<br>

On my machine the make process <span style="color: red"> failed</span>. Running make on the Makefile from the linuxkit I received the following error.

![Linuxkitmake error]({{ site.url }}/assets/3linuxkitmakeerr.png)

I will work on that error later and check whats wrong here. For me and now I decided to use the “go-way” building the binaries. Let's rock.

You can run the commands show in the linuxkit github repo.

{% highlight text %}
go get -u github.com/moby/tool/cmd/moby
get -u github.com/linuxkit/linuxkit/src/cmd/linuxkit
{% endhighlight %}

These commands are just downloading the files! They are NOT complied now. So, let’s do it.<br>
But wait?! Where are my sources? We did not specified any download path? Is it just magic? Now, not really. Have a look into to your Go envs. Navigate inside your GOPATH. Here they are. Go to 
 <b>src/github.com/</b><br>
 
Here you can see at least to folders. Moby and linuxkit. To build the binaries you can run the make command inside the moby or linuxkit directory (Lets keep it simple. The make command need a makefile. So let the make run where the Makefile is stored).

The make command will output and golint error at the first run.


![Linuxkitmake golint error]({{ site.url }}/assets/3linuxkitmakegoerr.png)

To fix this just download the golint form github with our old fried <b>go –get</b>.<br>
For more information, visit the github repo: [Golang @ github ](https://github.com/golang/lint)
{% highlight text %}
go get -u github.com/golang/lint/golint
{% endhighlight %}

Now you can run the make command inside the moby-directory again. You should have a moby.exe file in $GOPATH/work/bin/ now.

![Golint in binpath]({{ site.url }}/assets/3golint.png)

Congratulations! Moby and linuxkit are now working on your machine. Don’t trust me! I am a developer ;)  Better check it yourselfe.

Note: You sould close your active cmd / bash window after adding your go-pathes to your global system variables.

After this type moby and linuxkit after each other.

Moby shows:

![moby test]({{ site.url }}/assets/4mobyconsole.png)

Linuxkit shows:

![linuxkit test]({{ site.url }}/assets/4linuxkitconsole.png)

#### Step 4: Build an image with moby.
Just to make it simple, we use an example.yml file for our teste. You can find the examples in the cloned github repo of linuxkit. Go into the examples directory and copy the yml-file into a new created test directory.

So, now the “For Windows users only” things coming into the game!

The linuxkit / mobyproject for Windows is not integrated in the tools yet. That means you are not able to to start your image with linuxkit run. There is a Powershell Script to close that gap and launch your moby builded images.

Let’s start with building an iso to boot our HyperV-VM before we are looking into the powershell script.

I copied the docker.yml linuxkit example file into a new directory. For a better understanding you should read the file and understand it. There is one line very important for us.

![docker.yml example file]({{ site.url }}/assets/4dockerymledit.png)

To make the iso runnable or adoptable for HyperV we need to change the output format:

From kernler+initrd to iso-efi!
For more information see the docs @ github:

[https://github.com/linuxkit/linuxkit/blob/master/docs/yaml.md](https://github.com/linuxkit/linuxkit/blob/master/docs/yaml.md)

Now, we can start building our first moby-Image. Inside your docker example directory run

moby build docker (Or the name of your yml-file). Your output should look like this:

![moby build ]({{ site.url }}/assets/5mobybuild.png)

Congratulations, there is your first docker-efi.iso. Ready to create a HyperV-VM out from it.

#### Step 5. Start your moby-image in HyperV
As mentioned above, you <b>can’t</b> start your VM by typing linuxkit run. This is not supported, at the moment. Because of that you can find a script to create a HyperV-VM from the created iso-file.

You can find the script in the linuxkit/scripts folder.

It looks we need to create the VM first and can run it with LinuxKit.ps1 –Start. So, let’s create the VM.

For this, I am using a Powershell-Session and run the LinuxKit.ps1 Script with the minimal set of inbound parameters.

-IsoFile = Path to the moby created ISO-file <br>
-Create = To create the machine <br>


![docker.yml example file]({{ site.url }}/assets/6linuxkitpowershell1.png)

Your output should look like this.

![docker.yml example file]({{ site.url }}/assets/6linuxkitpowershell2.png)

Now, you can see the created VM in your HyperV-Manager. Let’s check it out.


![docker.yml example file]({{ site.url }}/assets/6hypervoff.png)

Here they are. Let’s start the VM. We will use the script again and type
{% highlight text %}
LinuxKit.ps1 –Start.
{% endhighlight %}

Let’s check the HyperV-Manager again.

![docker.yml example file]({{ site.url }}/assets/6hypervon.png)

The moby builded VM is no up and running!

{% if page.comments %}

<div id="disqus_thread"></div>
<script>
/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {

this.page.url = tippexs.github.io;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = docker-mlkit-windows-setup; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var disqus_developer = 1; // Comment out when the site is live
var d = document, s = d.createElement('script');
s.src = 'https://tippexs-github-io.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>


{% endif %}



<script id="dsq-count-scr" src="//tippexs-github-io.disqus.com/count.js" async></script>