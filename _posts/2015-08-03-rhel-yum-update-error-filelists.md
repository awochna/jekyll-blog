---
title: "Red Hat yum update error on filelists"
tags:
  - rhel
  - red hat
  - system
  - server
  - administration
  - error
  - yum
---

Just a quick heads up for those of you managing Red Hat Enterprise Linux servers out there that might run into the following error:

{% highlight bash %}
Error: failure: repodata/21902f8fd8b374a83d65f39dc78c9707a6c7d0274e8d6e7344cdcb958410cfa2-filelists.sqlite.bz2 from epel: [Errno 256] No more mirrors to try.
{% endhighlight %}

I've hit it twice now on different servers updating to 6.7.
Luckily the fix is really easy, just run:

{% highlight bash %}
$ sudo yum clean all
{% endhighlight %}

Yum will look for the data in the right place next time you try to run `sudo yum update`.
For more information, check out the [RHEL documentation on the yum cache](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Working_with_Yum_Cache.html)
Hope this helps you.

{% include dev-panel.html %}
