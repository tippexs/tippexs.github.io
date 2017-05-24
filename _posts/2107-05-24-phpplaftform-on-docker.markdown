---
layout: post
title:  "PHP Platform on Docker"
date:   2017-05-24 10:05:00 +0200
comments: true
categories: docker tutorial
---

# A PHP-Platform Docker tutorial to make PHP runnable in Docker

![PHP Running]({{site.url}}/assets/elephant.gif)


First of all: I LOVE PHP! I am PHP-Developer for more then 12 years now and still love it like on the first day ;)

But a new "Darling" shown up 4 years ago. DOCKER! I don't want to loos my PHP but I was also interested using Docker for my App-Servers and my Dev-Machine.

Because of this I decided to use PHP and Docker at the same time and what can I say: IT IS JUST AWESOME!

In this Tutorial i will show you the Setup on my Dev-Machine to develop PHP-Apps with PHP-FPM and NGINX.

#Prerequirements
You have Docker installed already and know how Dockerfiles work.

WARNING: This setup is just for your Dev-Machine! Do not use it 1 to 1 on your production servers! I will post a second tutorial how to build this on production!

#Lets go!

Basically, we need two Docker-Images: 
- PHP-FPM
- Nginx

I build my own PHP-FPM5 and 7 images based on Alpine. You should create two directories phpfpm5 and phpfpm7 and create the Dockerfiles in it.

#### Configfiles for php-fpm.

php-fpm.conf

{% highlight text %}
[www]
user = nobody
group = nobody
listen = [::]:9000
chdir = /application
pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
catch_workers_output = Yes
{% endhighlight %}


php.ini

{% highlight text %}
date.timezone = "UTC"
short_open_tag = Off
session.auto_start = Off
{% endhighlight %}


#### Dockerfile PHP-FPM5

{% highlight text %}

FROM alpine:3.5

RUN apk --update add \
        php5 \
        php5-bcmath \
        php5-dom \
        php5-ctype \
        php5-curl \
        php5-fpm \
        php5-gd \
        php5-iconv \
        php5-intl \
        php5-json \
        php5-mcrypt \
        php5-opcache \
        php5-openssl \
        php5-pdo \
        php5-pdo_mysql \
        php5-pdo_pgsql \
        php5-pdo_sqlite \
        php5-phar \
        php5-posix \
        php5-soap \
        php5-xml \
    && rm -rf /var/cache/apk/*

COPY php.ini /etc/php5/conf.d/50-setting.ini
COPY php-fpm.conf /etc/php5/php-fpm.conf

EXPOSE 9000

CMD ["php-fpm", "-F"]


{% endhighlight %}

#### Dockerfile PHP-FPM7

{% highlight text %}

FROM alpine:edge
RUN echo 'http://dl-cdn.alpinelinux.org/alpine/edge/testing' >> /etc/apk/repositories && \
    apk --update add \
        php7 \
        php7-bcmath \
        php7-dom \
        php7-ctype \
        php7-curl \
        php7-fileinfo \
        php7-fpm \
        php7-gd \
        php7-iconv \
        php7-intl \
        php7-json \
        php7-mbstring \
        php7-mcrypt \
        php7-mysqlnd \
        php7-opcache \
        php7-openssl \
        php7-pdo \
        php7-pdo_mysql \
        php7-pdo_pgsql \
        php7-pdo_sqlite \
        php7-phar \
        php7-posix \
        php7-session \
        php7-soap \
        php7-xml \
        php7-xmlreader \
        php7-xmlwriter \
        php7-zip \
    && rm -rf /var/cache/apk/*

COPY php.ini /etc/php7/conf.d/50-setting.ini
COPY php-fpm.conf /etc/php7/php-fpm.conf

EXPOSE 9000

CMD ["php-fpm7", "-F"]


{% endhighlight %}

Based on these Dockerfiles run a docker build command to build your images. Navigate into your directories and run

{% highlight text %}
 docker build -t tippexs/phpfpm7:1.0 .
{% endhighlight %}

{% highlight text %}
 docker build -t tippexs/phpfpm5:1.0 .
{% endhighlight %}

After this you should have two Docker images on your machine. You can see the new generated images by typing 


{% highlight text %}
 docker images
{% endhighlight %}

Your output should look like this:

{% highlight text %}
tippexe/phpfpm5        1.0                 818a198d3c00        6 days ago          82 MB
tippexe/phpfpm7        1.0                 21ccb78f50bb        6 days ago          67.7 MB
{% endhighlight %}


Before we run the PHP-Containers we should setup the NGINX-Webserver and link it to the PHP-FPM Container.

#### Build your NGINX Container
nginx configuration file
{% highlight text %}

server {
    listen       80;
    server_name  localhost;

    location / {
        root   /var/www;
        index  index.php index.html index.htm;
    }

    error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.(php)$ {
       	fastcgi_pass php:9000;
        fastcgi_param DOCUMENT_ROOT   /application;
        fastcgi_param SCRIPT_FILENAME /application$fastcgi_script_name;
	    include /etc/nginx/fastcgi_params;
    }


}

{% endhighlight %}

Dockerfile nginx

{% highlight text %}
FROM nginx:latest 
COPY conf/nginx.conf /etc/nginx/conf.d/app.conf
{% endhighlight %}

You can build your Docker Image in the same way you built your PHP-FPM Images. Navigate into the directory and run docker build. Don't forget to label your build ;).

#### Run your Containers

Make sure you have at least 2 images. NGINX and PHP-FPM.

Now we can run our containers. Very important at this point is the fact we need to link the containers against each other and allow them to talk.

First, start your PHP-FPM container. In my case PHP-FPM7. 

{% highlight text %}
 docker run --name php -d -v /var/www/myapphome:/application tippexe/phpfpm7:1.0
{% endhighlight %}

The --name is important to link our containers. I case this is completely new to you read the docs.

[Docker container networking](https://docs.docker.com/engine/userguide/networking/)

There was or still is a CLI-command --link. This i depracted and should not be used anymore. Therefore you should create a User-defined bridged network for your containers.

[Docker User-defined networks] (https://docs.docker.com/engine/userguide/networking/#user-defined-networks)

I did this by typing:

{% highlight text %}
 docker network create --driver bridge dev
{% endhighlight %}

Now you should have a User-defined bridged dev network (Isn't that a perfect word for hangman: "userdefinedbridgeddevnetwork" :D)

List your networks by typing docker network ls.

Now, finally!, let's start our containers.

Important: Start your PHP-FPM first.


{% highlight text %}
docker run --name=php --network=dev -itd -v /var/docker/audi_nginx/apphome:/application tippexs/phpfpm7:1.0
{% endhighlight %}

Now, start your NGINX webserver

{% highlight text %}
docker run --network=dev -itd -p 80:80 --name=web -v /var/www/mywebapps/apphome:/var/www tippexs/nginx:1.0
{% endhighlight %}

Let's have a look if it works: 

Type docker ps to list your running container instances. You should have two containers up & running.

{% highlight text %}
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
072945924b22        tippexs/phpfpm5:1.0    "php-fpm -F"             55 seconds ago      Up 55 seconds       9000/tcp             php
b4bc295200e7        tippexs/nginx:1.0      "nginx -g 'daemon ..."   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp   web

{% endhighlight %}

Awesome! That looks good. Now lets see what webapp we are running, currently.

![docker webapp example image]({{ site.url }}/assets/dockerphp_webapp.png)

To change the PHP-FPM for example just kill and remove the running PHP-FPM container instance and run the other PHP-FPM container.

#### How to do this?
simply run docker ps to list your running containers (As we did it a lately to check our running containers). Each running container has an unique container-id. To kill and remove the container you can run
{% highlight text %}
docker kill 072945924b22 && docker rm 072945924b22
{% endhighlight %}


#### What's next
1. A PHP and Docker production Tutorial i a new Post.
2. Use Pear and Pecl with PHP-FPM on alpine-linux Base-Images

If you have any questions or commands feel free to leave a comment.



{% if page.comments %}

<div id="disqus_thread"></div>
<script>
/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {

this.page.url = tippexs.github.io;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = phpplatform-on-docker; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://tippexs-github-io.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>


{% endif %}



<script id="dsq-count-scr" src="//tippexs-github-io.disqus.com/count.js" async></script>