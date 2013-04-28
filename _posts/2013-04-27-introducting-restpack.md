---
layout: blog_post
title: Introducing RestPack
---

<h3>Services, lots of 'em</h3>

<p>
  Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
  tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
  quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
  consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
  cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
  proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
</p>

<blockquote>
  <p>Views in Ember.js are typically only created for the following reasons:</p>

  <ul>
    <li>When you need sophisticated handling of user events</li>
    <li>When you want to create a re-usable component</li>
  </ul>
</blockquote>

<p>
  Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
  tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
  quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
  consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
  cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
  proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
</p>

{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}



{% highlight ruby %}
module RestPack::Serializer::SideLoading
  extend ActiveSupport::Concern

  module ClassMethods
    def side_loads(models, options = {})
      side_loads = {}
      return side_loads if models.empty? || options[:includes].nil?

      options[:includes].each do |include|
        side_loads.merge! side_load(include, models, options)
      end
      side_loads
    end

    private

    def side_load(include, models, options)
      association = association_from_include(include)

      if supported_association?(association)
        serializer = RestPack::Serializer::Factory.create(association.class_name)
        return send("side_load_#{association.macro}", association, models, serializer)
      else
        return {}
      end
    end

    def supported_association?(association)
      [:belongs_to, :has_many].include?(association.macro)
    end

    def side_load_belongs_to(association, models, serializer)
      foreign_keys = models.map { |model| model.send(association.foreign_key) }.uniq
      side_load = association.klass.find(foreign_keys)

      return {
        association.plural_name.to_sym => side_load.map { |model| serializer.as_json(model) }
      }
    end

    def side_load_has_many(association, models, serializer)
      return {} if models.empty?
      return serializer.class.page({
        filters: { association.foreign_key.to_sym => models.map(&:id) }
      })
    end

    def association_from_include(include)
      possible_relations = [include.to_s.singularize, include.to_s.pluralize]
      possible_relations.each do |relation|
        association = self.model_class.reflect_on_association(relation.to_sym)
        return association unless association.nil?
      end

      raise RestPack::Serializer::InvalidInclude.new,
        ":#{include} is not a valid include for #{self.model_class}"
    end
  end
end
{% endhighlight %}