---
layout: post
title:  "Softwaredevelopment and Docker"
date:   2017-05-19 21:05:00 +0200
comments: true
categories: docker tutorial
---

# Softwaredevelopment and Docker - Audi Cloud Day Presentation
## Is the Cloud changing the process of softwaredevlopment?


In fact, yes it changed a lot during the last years.

I was able to speak at the Audi Cloud-Day, yesterday and introduced softwaredevelopment in the cloud and the Docker basics.

The PDF is the presentation. There were a couple of basic questions. Let me name a few here:

- Why are Docker container so different from virtual machines?! They have a OS included, as well?!
- How can I build containers and what are layers?
- Can I use a custom OS-Image (patched, customized, ... ) and create a Docker Base-Image form it?

For these people asked that questions - give me a couple of days, please. I will update my Blog and share the Information with you and others here as soon as possible.

[get the PDF]({{ site.url }}/assets/swdevanddocker.pdf) 

{% if page.comments %}

<div id="disqus_thread"></div>
<script>
/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {

this.page.url = tippexs.github.io;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = do; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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