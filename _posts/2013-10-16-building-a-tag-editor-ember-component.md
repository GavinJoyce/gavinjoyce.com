---
layout: blog_post
title: Building a tag editor with Ember.js
tags: emberjs video
---

###tl;dr: Ember.js components are amazing

For the past while I've been helping <a href="http://songspace.com/">songspace.com</a> build an ambitious web application with <a href="http://emberjs.com/">Ember.js</a>. This is the fifth significant Ember application that I've worked on, and I'm often still amazed at just how well Ember deals with complexity.

---

<blockquote class="twitter-tweet"><p>Just replaced a 200 line jQuery tag editor plugin with a 20 line <a href="https://twitter.com/search?q=%23EmberJS&amp;src=hash">#EmberJS</a> component. Amazing.</p>&mdash; Gavin Joyce (@gavinjoyce) <a href="https://twitter.com/gavinjoyce/statuses/377901721017528320">September 11, 2013</a></blockquote>
<script async="true" src="//platform.twitter.com/widgets.js" charset="utf-8">
</script>

---

<a href="http://emberjs.com/guides/components/">Ember Components</a> are one of many reasons for this, they very neatly allow us to create decoupled views which can be reused throughout our application. Below tag editor component which consists of 30 lines of clean JavaScript. (view the <a href="http://jsbin.com/eQOZoGe/302/edit" target="_blank">source on jsbin</a>)

### Live demo:

<a class="jsbin-embed" href="http://jsbin.com/eQOZoGe/302/embed?output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.34.3"></script>

### 30 lines of simple, focused JavaScript:

{% highlight javascript %}
App.TagEditorComponent = Ember.Component.extend({
  keyDown: function(e) {
    if(e.which === 13) { this.send('add'); }
  },
  focus: function() {
    this.$('input').focus();
  }.on('didInsertElement'),
  actions: {
    add: function() {
      var newTags = this.get('newTags');
      if(newTags) {
        var tags = this.get('tags');
        newTags = newTags.split(',').map($.trim);

        newTags.forEach(function(tag) {
          if(tag.length > 0 && !tags.contains(tag)) {
            tags.pushObject(tag);
          }
        });
        this.set('newTags', '');
        this.focus();
      }
    },
    remove: function(tag) {
      var tags = this.get('tags'),
          index = tags.indexOf(tag);
      tags.removeAt(index);
      this.focus();
    }
  }
});
{% endhighlight %}


### <a href="http://jsbin.com/eQOZoGe/302/edit">View code on jsbin.com >></a>
