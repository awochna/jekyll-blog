---
title: "Drupal Views Partials: Nested Views"
tags:
  - drupal
  - views
  - technical
---

So if you're working with complex data sets or some sort of workflow system in Drupal, you've likely encountered the difficulties in trying to extract that information using views over multiple nodes using a single unique value.
For me, recently, it was a site that uses a lot of webforms to collect information from users (with accounts) that need to be viewed by another type of user.
These administrative users would want to view who submitted Webform A, with a link to view the submission, but they want to be able to see more information about the user than the standard "/node/[nid]/webform-results" would give them.
A view would be perfect; just collect all of the webform results into a table and add a filter for the Content: Title so you only get Webform A.
But then, they would like to know if the user *also* completed Webform 2, because that form is an important part of the workflow and process that Webform A represents.
What do you do?

* You can't remove the Content: Title filter.
  * It would just show all webform submissions, including submissions that aren't related to this set.
  * It would also turn the submission from Webform 2 into rows in the table, not showing their implicit relationship (by the user) with the submissions from Webform A.
* You can't change the relationships around to render users and pull their Webform submissions.
  * This would also show all of the users who have never submitted a webform.
  * You could get a lot of repeat results, depending on how you set the Format.
  * You are going to have a hard time getting the filters to apply properly (you'll have to use and/or in the Filter Criteria).
  * Both Webform A and Webform 2 results are still going to be *separate rows* in the table, leaving that implicit relationship out.

Don't you wish you could just rewrite complex sections like this into a rails application so you have more control over stuff like this?
Just me?
Okay.

## Enter View: Include View ##

This is a free feature you get when you install the [Views PDF](https://www.drupal.org/project/views_pdf), which this site has for other workflows and features.

When you are adding fields to a view, you can actually include another view!
No, it is not called View-ception. Please, just don't.
When you click 'Add' under the fields section, you can select 'View: Include View' to include a whole other view into your current view.
They are listed by their machine name.

### Side note ###

When I say "whole other view," I mean that you can't just pick a different display of the current view to add in.
I've wanted to do this, but you can't.

Now, you just have to create another view that shows webform submissions, has the Submitted User relationship, and the UID contextual filter (argument).
Add in the filter on content title to get the right webform.
When you go to include it in the original view, you can pass the submitter's UID as an argument to the included view.

Now it just works!
