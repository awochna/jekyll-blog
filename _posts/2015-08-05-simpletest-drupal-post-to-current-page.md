---
title: "Simpletest: Fill out a form and submit it"
tags:
  - drupal
  - simpletest
  - tdd
  - bdd
  - module development
---

So I like building my custom modules in the test first fashion.
In Drupal, to test de-coupled site functionality from custom modules, it's conventional (and pretty easy) to use the built-in Simpletest.
It provides some pretty neat Drupal-specific functions to help with creating and logging in users, creating nodes, etc.
However, some of these functions are a little less than well-documented, but hey, it's PHP.
TDD is a product of my Ruby background because automatic test are valued in that community of developers, as evidenced by the many libraries and some of the test styles even creeping into other language communities, like [Cucumber](https://cucumber.io).
In PHP, TDD is treated differently and isn't as important, at least historically.
So it only makes sense that the documentation would be a little weak, but anyway.

When testing something, it's important to try to decouple as much as possible from the test.
You don't want to put in hard-coded values that could end up changing and breaking half of your test suite, only to be fixed by a thorough copy/replace job.
Since Simpletest is using a virtual browser to perform your tests, you want to reduce hard-coded dependencies as much as possible, to the point that you want to avoid using URL paths if you don't have to.
For example, in your test, say you have an authenticated user who starts on a page (one hard-coded URL), but then clicks to another page with a form.
You want them to be able to fill out that form and submit some information, creating a post request.
You don't want to have to say what the URL of that form page is, it shouldn't matter at all.
If you need to change it from `thing/new` to `thing/create` in the future, it shouldn't break this test.
The user can still submit the form.

The function call to do this is drupalPost(), but take a look at it's signature:

{% highlight php %}
<?php
function $this->drupalPost($path, $edit, $submit, array $options = array(), array $headers = array())
{% endhighlight %}

It needs the path to send the post request to, the array of fields and values (`$edit`), and the name of the submit button (`$submit`).
It seems like you need to hard code that URL into this request, although *it shouldn't matter*.

The secret lies in the last sentence for [the documentation for this API function](https://www.drupal.org/node/265762):

> With multi-step forms, it is possible to post each subsequent step by passing NULL as the $path.

So with a bit of experimentation, you can fill out the current page by calling the function like so:

{% highlight php %}
<?php
$this->drupalPost(NULL, $edit, 'Submit');
{% endhighlight %}

And it'll use the current page.
No more hard-coded URL's!

{% include dev-panel.html %}
