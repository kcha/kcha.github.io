---
layout: post
title: Building my website with Jekyll
---

Recently I decided to update my personal website using [Github Pages](https://pages.github.com/) and [Jekyll](http://jekyllrb.com/). One big selling point was that I could use [Markdown](https://daringfireball.net/projects/markdown/) to create my pages and posts. I was
 inspired by this [blog post](http://joshualande.com/jekyll-github-pages-poole),
which was a great starting point for me to dive into Jekyll. Here, I
write about my own experience getting started. Rather than writing a comprehensive how-to post, 
I'll just point to online resources that I learned from and highlight some
troubleshooting items that I encountered.

# Installation

See [here](http://jekyllbootstrap.com/lessons/jekyll-introduction.html) for a nice
overview of what Jekyll is and how it works.

To install Jekyll on my Mac, I followed
the instructions on the official [website](http://jekyllrb.com/docs/installation/).
There were a few pre-requisites that I had to take care of first.
I first had to update [RubyGems](https://rubygems.org/pages/download) and
update my Xcode Command-Line Tools.

{% highlight sh %}
$ gem update --system
$ xcode-select —install
{% endhighlight %}

I also needed to install [NodeJS](https://nodejs.org/en/).

# Choosing a theme

[Jekyll Themes](http://jekyllthemes.org/) is a great place to start looking for themes. 
One theme that looks great is [Beautiful Jekyll](https://github.com/daattali/beautiful-jekyll). I eventually decided to start with a simple and minimalistic look using 
[Poole](http://getpoole.com/). 

# Troubleshooting

With Jekyll 3.0, there were some changes that affected the rendering of Poole.
As of this writing, it seems that Poole hasn't been updated to be [compatible with Jekyll 3.0](https://github.com/poole/poole/issues/99).

When I tried to serve the website for the first time, I ran into some errors:

{% highlight text %}
$ jekyll serve
Configuration file: /Users/kevinha/Dropbox/website/poole/_config.yml
       Deprecation: You appear to have pagination turned on, but you haven't included the `jekyll-paginate` gem. Ensure you have `gems: [jekyll-paginate]` in your configuration file.
            Source: /Users/kevinha/Dropbox/website/poole
       Destination: /Users/kevinha/Dropbox/website/poole/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
Since v3.0, permalinks for pages in subfolders must be relative to the site source directory, not the parent directory. Check http://jekyllrb.com/docs/upgrading/ for more info.
{% endhighlight %}

I found the solution [here](https://github.com/poole/poole/issues/112). I had to
install the missing gem for paginate as it is no longer included in 3.0. I also
installed gist as I'll probably use it in the future.

{% highlight sh %}
$ gem install jekyll-paginate
$ gem install jekyll-gist
{% endhighlight %}

I next made some changes in my `_config.yml` file. The first was to add
the installed gems by adding the following lines:

{% highlight yaml %}
gems:
  - jekyll-paginate
  - jekyll-gist
{% endhighlight %}

Lastly, I removed the `relative_permalinks: true` line.

# Customizations

## Navigation bar
A lot of my additional customizations were taken from [here](http://joshualande.com/jekyll-github-pages-poole/), which included how to add a navigation bar. I also made a few other adjustments, including right-justifying
the navigation panel. To right-justify, I followed the changes from this user's [GitHub Page](https://github.com/eka-putra/eka-putra.github.io).

First, I enclosed the navigation bar with a `<nav>` HTML tag in `default.html`:

{% highlight html %}
{% raw %}
<nav>
  {% for page in site.pages_list %}
    &nbsp;&nbsp;&nbsp;<small><a href="{{ page[1]  }}">{{ page[0] }}</a></small>
  {% endfor %}

  &nbsp;&nbsp;&nbsp;{% include icons.html %}
</nav>
{% endraw %}
{% endhighlight %}

Then, I added the float attribute to the CSS file `_masthead.scss`:

{% highlight css %}
.masthead-title, nav {
  float: left;
  ...
}

nav {
  float: right;
}
{% endhighlight %}

For fun, I also included some icon links using [Font Awesome](https://fortawesome.github.io/Font-Awesome/) in the footer. I created a new HTML snippet called `icons.html` and saved it inside the `_includes` sub-directory. This [blog post](https://blog.r3bl.me/en/simple-social-media-links-jekyll/) provides a nice explanation on how to integrate
social media links using Jekyll. Beautiful Jekyll also uses a similar approach.
Here, I'm only showing icons for Home
and GitHub, but I had also included others like e-mail and RSS in the real file.

{% highlight html %}
{% raw %}
<ul class="footerbar">
  {% if site.author.url and site.icons_list.home %}
  <li>
    <a href="{{ site.baseurl }}/" title="Home">
      <i class="icon fa fa-home fa-lg"></i> 
    </a>
  </li>
  {% endif %}
  {% if site.github_username and site.icons_list.github %}
  <li>
    <a href="https://github.com/{{ site.github_username }}" title="GitHub">
      <i class="icon fa fa-github fa-lg"></i>     
    </a>
  </li>
  {% endif %}
</ul>
{% endraw %}
{% endhighlight %}

To control which icons get shown, I added a custom variable `icons_list` into
_config.yml`:
{% highlight yaml %}
icons_list:
  home: true
  rss: false
  email: true
  github: true
  linkedin: false
{% endhighlight %}

Be sure to add all the required site variables for this to work. For example:

{% highlight yaml %}
author:
  name:              <your name>
  email:             <your e-mail>
  url:               <your url>

github_username:     <your-username>
{% endhighlight %}

Lastly, I added hover effects to the navigation items by borrowing some CSS code 
from a Bootstrap theme I previously used.

{% highlight css %}
  a {
    color: #505050;
    -moz-transition: color 0.15s ease-in-out;
    -webkit-transition: color 0.15s ease-in-out;
    -o-transition: color 0.15s ease-in-out;
    -ms-transition: color 0.15s ease-in-out;
    transition: color 0.15s ease-in-out;
  }

  a:hover {
    color: #C6C8C9;
  }
{% endhighlight %}

## Creating `index.html` from Markdown
Since I wanted my front page to be my "About Me" page. I replaced the default 
`index.html` with a Markdown version `index.md`, where I could write my profile in Markdown.
Jekyll automatically parses this file and creates an `index.html` file when 
serving or building the site.

## Changing blog location
I wanted to keep my blog in a sub-directory.
A quick Google search led me to this [blog post](http://joshbranchaud.com/blog/2013/03/02/Running-Your-Jekyll-Blog-from-a-Subdirectory.html), which was
really helpful. Following Solution 2, I created a new sub-directory called
`blog` and added a new index file within that sub-directory. Then, in `_config.yml`,
I added a line to set how I want the blog post URLs to look like:

{% highlight yaml %}
permalink:          blog/:title.html
{% endhighlight %}

In this case, I wanted my URL to include the `blog` subdirectory, followed by the
title of the blog post\*. You could also include
the date in the filename as well by doing something like:

{% highlight yaml %}
permalink:          blog/:year/:month/:day/:title.html
{% endhighlight %}

*\*Update: I actually decided to use this latter format to
make it easier to see the date of posting in the URL*.

## Adding a favicon
I chose a [Font Awesome](http://fontawesome.io/) favicon (<i class="icon fa fa-code fa-lg" title="code favicon"></i>)
from [here](http://paulferrett.com/fontawesome-favicon/) and added it into `head.html`.
