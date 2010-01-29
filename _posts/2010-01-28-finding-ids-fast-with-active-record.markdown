---
layout: post
title: "Finding ids and other column values fast with ActiveRecord"
---

Chances are at some point you've wanted to get the values of a single column from ActiveRecord.  You might have done something like this:

{% highlight ruby %}
user_ids = User.find(:all).map(&:id)
{% endhighlight %}

That's great and all, but hopefully somebody points out that you can pass ActiveRecord#find a :select key.

{% highlight ruby %}
user_ids = User.find(:all, :select => 'id').map(&:id)
{% endhighlight %}

Now we're getting somewhere.  On a "wide" table (ie. one with a lot of columns, large columns, or binary types stored separately) this alone can be a huge improvement, as just sending all that data across the wire can really waste time.  While ActiveRecord is generally good enough performance wise, sometimes getting a little closer to the metal is appropriate, and in comes find_ids.

{% highlight ruby %}
user_ids = User.find_ids
{% endhighlight %}

### Benchmarks
Here's some [benchmarks](http://github.com/stopdropandrew/find_ids/blob/master/benchmarks/benchmarks.rb) against a fairly "wide" table with 100 records, 10,000 times for each style:

                                  user     system      total        real
    find(:all).map           26.460000   3.130000  29.590000 ( 72.656614)
    find(:all, :select).map  12.770000   0.560000  13.330000 ( 16.232474)
    find_ids                  7.870000   0.340000   8.210000 ( 10.465719)

### Examples 
But that's not all, it also natively supports other columns, associations, and named scopes.

{% highlight ruby %}
emails = User.find_column_values('email')
User.wristbands.find_ids
User.scoped(:order => 'created_at desc').find_ids
{% endhighlight %}

### Installation

script/plugin install git://github.com/stopdropandrew/find_ids.git

### Issues

It currently only works with MySQL, but submissions for other adapters are [welcome](http://www.github.com/stopdropandrew/find_ids).
