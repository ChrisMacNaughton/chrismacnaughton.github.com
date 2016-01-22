---
layout: post
title: "User Generated Templates in Ruby"
description: "Using Liquid templates in a ruby application to handle nested, user generated content."
category: Ruby
blog: true
---
# User Generated Templates in Ruby (with Liquid)

I started a side project recently for hosting many sites from within one application. I decided to use Liquid to allow users to create their own site designs. [Shopify](https://www.shopify.com/) designed [Liquid](http://liquidmarkup.org/) to give users design freedom without compromising server integrity.

In the Rails app, I have a `Theme` and a `Template` model. A theme has many templates, and templates contain the Liquid markup in a `content` attribute. A theme has a primary template. To render a theme, we use `liquid_template = Liquid::Template.parse(primary_template.content)` to parse the Liquid content. This call is cacheable and so it is in the app.

After we parse the primary template, we render it with `liquid_template.render(options, registers: {file_system: LiquidTemplateSystem.new(self)})`. The LiquidTemplateSystem is a class I wrote to handle the nested template functionality. The goal is to call `{{% include 'subtemplate' %}} inside one template to render another template's content.

{% highlight ruby linenos %}
class LiquidTemplateSystem
  attr_reader :theme
  def initialize(theme)
    @theme = theme
  end

  def read_template_file(template_path, context)
    theme.templates.where(name: template_path).first.content
  end
end
{% endhighlight %}

The initializer just stores the theme that the we initialize the class with. Our file_system instance responds to the method `read_template_file` to handle the actual `include` call.  `read_template_file` can accept one or two arguments (take two!). `read_template_file` must return a valid Liquid string that will render into the including template.