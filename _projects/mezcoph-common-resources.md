---
title: MEZCOPH Common Resources
order: 95
start_date: 01/2015
end_date: 02/2015
major_features:
  - Ruby on Rails
  - Administrative interface using active_admin
  - Test-driven development using RSpec3, Capybara, and Cucumber
  - All other responses are json and xml
  - Central source of directory information for the college's many products
  - Presented at UA Web Developer's group meeting on 2015-02-11
image:
  url: /assets/mezcoph-common-resources.png
  alt: This application was API only, so no screenshot is available.
---

This was one of the first applications I worked on developing for the college of public health.
They have a number of sites for projects, centers, and sections, all with their own directory of a subset of the college's employees.
To help ease updating that directory, we wanted to build something that would act as a central authority on directory information.
Rails was a great choice, given my prior experience and its' emphasis on testing first.

Given that all of the user-facing output of the application was machine readable code, writing good tests first ensured that we would have a solid API, protected against regressions.

This application was featured in a presetation given to the UA Web Developer's group on February, 11th, 2015.
The [presentation is still available online](http://prezi.com/3xfe9irhcfug/?utm_campaign=share&utm_medium=copy) if you are interested in viewing it.
