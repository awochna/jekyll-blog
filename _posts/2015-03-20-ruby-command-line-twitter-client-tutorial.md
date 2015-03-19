---
title: "Tutorial: Ruby comand line twitter client"
tags:
  - ruby
  - technical
  - twitter
  - command line
  - open source
  - tutorial
---

Have you ever wanted to post a tweet from a command line?
If you're like me, you want to use twitter, but keep getting distracted by all the other tweets from the people you follow.
(That's not just me, right?)
And, if you're like me, you would like to use sferik's command line suite '[t](https://github.com/sferik/t)' but you've already got a utility named `t` on your command line.
(I'm sure that's also not just me, right?)

So, let's try building one from scratch in ruby using the [twitter gem](https://github.com/sferik/twitter) and a cool command line framework.
We can use [thor](https://github.com/erikhuda/thor) since it's popular and stable.
We'll avoid doing anything other than manual testing with this application.
After all, we're directly interfacing with a public API (your twitter account) and we're the only ones using it anyway.
Normally I'm pretty OCD about testing, but in these circumstances, I'm much more relaxed, especially since our dependencies will be doing most of the work.

## Time to start ##

We're going to need a new folder to store our project in, so make a new one in your 'projects' directory (or wherever your store your code).

{% highlight bash %}
$ cd ~/projects
$ mkdir twitter-cli && cd twitter-cli
{% endhighlight %}

Now you need [ruby installed](http://rvm.io) for this next bit, we need to create a Gemfile and use the thor and twitter gems.
Create a new file named `Gemfile` in this project's directory.

{% highlight ruby %}
source 'https://rubygems.org'

gem 'thor', '0.19.1'
gem 'twitter', '5.14.0'
{% endhighlight %}

Now we can just run `bundle install` and we'll get the requirements for our application.

To be good code citizens, we should also really, **really** use some sort of source control for our code.
I know it's just us building and using this thing, but I have saved myself a number of times by source controlling things like these.
Not to mention, it can make your workflow just faster and easier.
It's good practice.

{% highlight bash %}
$ git init
$ git add Gemfile Gemfile.lock
$ git commit -m "Initial commit of code"
{% endhighlight %}

You can find my git repository on [Github](https://github.com/awochna/twitter-cli).

Now we can start coding.
Make a new `tw` file:

{% highlight ruby %}
#!/usr/bin/env ruby

class TwitterCLI < Thor

end
{% endhighlight %}

This makes our new TwitterCLI class a child of Thor, inheriting all of its command line goodness.
There's just one problem.
If we try to run this right now, (make sure your file is executable `chmod +x tw`) we'll get an error!

{% highlight bash %}
$ ./tw
./tw:3:in '<main>': unitialized constant Thor (NameError)
{% endhighlight %}

Don't worry, nothing is broken, we just haven't the `Thor` constant.
Reading error codes and stack traces is a big chunk of software development.
(The other big chunk is googling them and the biggest one is understanding it all.)
To fix this, we just have to add a `require` statement to the top of the file:

{% highlight ruby %}
#!/usr/bin/env ruby

require 'thor'

class TwitterCLI < Thor
{% endhighlight %}

Now we're ready to build out some logic.
It's really easy in thor to add a command to your cli, each pubilc method is a new command.
You can also add in the descriptions for each command (available when you ask the cli for help) by using the `desc` method before each public method declaration.
Let's stub one out.

{% highlight ruby %}
#!/usr/bin/env ruby

require 'thor'

class MyCLI < Thor

  desc "post TWEET", "post to your twitter with TWEET"
  def post(tweet)
    # tweet posting logic goes here
    puts "#{Time.now} Posted: #{tweet}"
  end
end
{% endhighlight %}

We've left out the tweet posting logic and included a line to let us know what was tweeted and when.
This will help give us some initial feedback for testing.
However, we can't start testing it yet. We have to initialize our class properly.
At the bottom of the file, make one final addition to finish off our first interation:

{% highlight ruby %}
end

TwitterCLI.start(ARGV)
{% endhighlight %}

This runs the class, passing the command line arguments to it, using the start method declared by the parent class, Thor.

With that in place, we have finished an MVP (Minimum Viable Product) of our tool, it just doesn't do anything useful yet.
We can pass the tweet to our class, but we're the only twitter users that can read it.
That qualifies, right?
It just feels like starting on twitter again, or, if you're me, still being a few years into twitter.
Enough bragging, lets actually get it to post to our account so our followers, nad the world, can see our tweets too.

## Make a new twitter app ##

In order to post as ourselves, we can't just pass our username and password to twitter with every request.
That would be **highly** insecure.
Instead, twitter has apps, make one at [https://apps.twitter.com](https://apps.twitter.com).
After creation, you will be taken to the application settings page.
Here, you need to change your Access level by clicking on 'modify app permissions'.
I changed mine to "Read, Write and Access direct messages" in case I want to expand the capabilities later.
It only prevents me from having to look at this page again and get new access tokens.

Now we want to be able to store the keys and secrets for our Twitter app in the command line application we're building, but these things are sensitive data that you don't want leaked out, so **they need to stay out of version control**.
We need to add a new line in our `.gitignore` file.

{% highlight bash %}
access_tokens.yml
{% endhighlight %}

Now with that, we can safely create a new file (access_tokens.yml) and keep it out of source control.

{% highlight yaml %}
---
consumer_api_key: "YOUR_CONSUMER_KEY"
consumer_api_secret: "YOUR_CONSUMER_SECRET"
access_token: "YOUR_ACCESS_TOKEN"
access_token_secret: "YOUR_ACCESS_SECRET"
{% endhighlight %}

Now we need to read this configuration into our command line app.
Instead of reading it into our post method, let's create a new method just for reading configuration.
It should be placed after the existing `post` method, but before the `end` that closes the class.

{% highlight ruby %}
end

private

def read_config
  access_tokens = YAML.load_file(File.join(__dir__, 'access_tokens.yml'))
  client = Twitter::REST::Client.new do |config|
    config.consumer_key        = access_tokens['consumer_api_key']
    config.consumer_secret     = access_tokens['consumer_api_secret']
    config.access_token        = access_tokens['access_token']
    config.access_token_secret = access_tokens['access_token_secret']
  end
  return client
end
{% endhighlight %}

This adds a new, private method called `read_config` that reads through the `access_tokens.yml` file.
We use `File.join` to grab the directory our program is running in (`__dir__`) and add access_tokens.yml so that we can use the program anywhere on the command line without having to have the access_tokens.yml file in each directory.
Then, it uses that information to configure a new instance of the twitter gem's REST API client (Twitter::REST::Client).
Finally, it returns the client explicitly.

Make sure the method is private, otherwise, it will be listed as a possible subcommand when you try using the client without a subcommand.
To use the `YAML` constant, we need to add a new require statement at the top of our file:

{% highlight ruby %}
require 'thor' 
require 'twitter'
require 'yaml'
{% endhighlight %}

Now that all of our requirements are set up and we have the twitter gem in our file, let's add in that tweet logic.
First, let's make use of the new `read_config` method to create and configure the instance of `Twitter::REST::Client` that the application will use.

{% highlight ruby %}
def post(tweet)
  # tweet posting logic goes here
  client = read_config
  puts "#{Time.now} Posted: #{tweet}"
end
{% endhighlight %}

This new line (`client = ...') takes the return value of our read_config method and puts it into the client variable.
In this case, that's an initialized instance of the `Twitter::REST::Client` class.
With that client built and stored in the `client` variable, we manipulate it like any other ruby object.
Now, we just need to make use of the `#update` method that the twitter gem has built into the class.

{% highlight ruby %}
def post(tweet)
  # tweet posting logic goes here
  client = read_config
  client.update(tweet)
  puts "#{Time.now} Posted: #{tweet}"
end
{% endhighlight %}

If you test it now, by running:

{% highlight bash %}
$ ./tw post "Testing my ruby command line twitter client"
{% endhighlight %}

It will post perfectly fine!

## One last touch up ##

There's just one problem with our code now, the `puts` line.
Normally, a line like this is to give the user feedback to let them know that their command didn't just disappear into the aether.
However, for us, we aren't catching the cases where updating your status fails.
It'll still print out the message that the tweet was posted, whether it was or not, and could be confusing for the user.
Even if that user is us, I wouldn't trust myself to remember this little quirk in 3 months.
And, it's not good practice and that bothers me.

Let's take advantage of the fact that the `#update` method of `Twitter::REST::Client` returns the tweet object (`Twitter::Tweet`) once it has been posted.
This `Twitter::Tweet` has a `#text` method that will just be the text of our tweet.

{% highlight ruby %}
def post(tweet)
  # tweet posting logic goes here
  client = read_config
  posted_tweet = client.update(tweet)
  puts "#{Time.now} Posted: #{posted_tweet.text}" if posted_tweet.text == tweet
end
{% endhighlight %}

Finally, just add the `tw` file to your PATH or symlink it to somewhere in your PATH.
For me, I have a `bin` directory in my home folder for small tools I make that is in my path:

{% highlight bash %}
$ ln -s ~/projects/twitter-cli/tw ~/bin/tw
{% endhighlight %}

Now, you should be good to go! Happy tweeting!

If you liked this tutorial, or have some feedback, use this tool to send me a mention (@tidy_fox_dev).
It just might encourage me to make more tutorials in the future.
