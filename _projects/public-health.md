---
title: Public Health
start_date: 03/2014
major_features:
  - Drupal 7
  - Boost static asset caching (pages load in 1/3rd the time)
  - Omega 4, bootstrap-sass, and compass made theming robust and easy
  - Migration of almost 2000 nodes (pages and other content)
  - Views with entity references connect related content in ways that can be exposed to visitors
  - Context aware pieces to help users dig into more information about faculty, research areas, and research projects
  - Largest site to date
  - Large set of 'customers' involved and affected, having to balance their requests with each other and the reality of user experience.
  - Word-like (WYSIWYG) editor
  - Mobile responsive
  - One of the first sites to launch with the new UA brand guidelines, helping to shape standards before they were made.
image:
  url: /assets/public-health.png
  alt: Public Health screen shot
---

This is one of the major endeavors of my current position at the Mel and Enid
Zuckerman College of Public Health. Originally, the site ran on Drupal 6 with
a terrible page load time for logged-in users. Pages and functions were hacked
together with terrible bloat from unnecessary tags and pasting from Word. Some
built in functions were even built as custom modules.

All the content had to be ported over, including news article archives and old
research projects. This was done using the feeds and feeds tamper Drupal modules in a reliable and consisten manner.
At the same time, the automatic import and controlled tampering with data gave
a unique opportunity to improve the fundamental objects being imported and
give them obvious relationships. For instance, a faculty member is clearly
related to the research projects they have.

Now, these relationships are known to Drupal and are exposed through views on
the different node templates using Panels. This required some advanced views
configuration that I did not understand prior to the start of the project.
This would be only one of a number of things I learned during the development
of this site.

I have not put an end date on this project yet because, although it is live,
there are still things we want to improve upon. The primary rounds of
development, however, ended in October 2014.
