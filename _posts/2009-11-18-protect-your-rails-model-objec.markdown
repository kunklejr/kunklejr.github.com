---
layout: post
title: Protect your Rails Model Objects with Grant
date: 2009-11-18 14:00:00 -05:00
comments: false
tags: ruby security
---
Near Infinity recently announced the release of [Grant](http://github.com/nearinfinity/grant), a Ruby on Rails plugin for securing and auditing access to your Rails model objects, and I'm here to tell you a little bit about it. There are two primary pieces of Grant, model security and model audit. I'll be focusing on model security for this post and will address model audit in a later entry.

Grant's model security is deliberately designed to force the developer to make conscious security decisions about what CRUD operations a user should be allowed to perform on your model objects. It doesn't care how you choose to authenticate and authorize your users to perform a CRUD operation, it only cares that you actually do it.

Rather than specify which operations are restricted, Grant restricts all CRUD operations unless they're explicitly granted to the user. It also restricts adding or removing items from *has_many* and *has_and_belongs_to_many* associations. Only allowing operations explicitly granted forces you to make conscious security decisions. While it obviously can't ensure you make the correct decisions, it should help ease the latent fear that you've inadvertently forgotten to secure something.

Enough talk, let me show you an example of how you might use it. To enable model security you simply include the Grant::ModelSecurity module in your model class. In this example you see three grant statements. The first grants find (aka read) permission to everyone. The second example grants create, update, and destroy permission when the passed block evaluates to true, which in this case happens when the model is editable by the current user. You can put any code you want in that block as long as it returns a boolean value. Similarly, the third grant statement permits additions and removals from the tags association when it's block evaluates to true. A Grant::ModelSecurityError is raised if any grant block evaluates to false or nil.

``` ruby
class EditablePage < ActiveRecord::Base
  include Grant::ModelSecurity
  has_many :tags

  grant(:find) { true }
  grant(:create, :update, :destroy) do |user, model| 
    model.editable_by_user? user 
  end
  grant(:add => :tags, :remove => :tags) do |user, model, associated_model| 
    model.editable_by_user? user 
  end

  def editable_by_user? user
    user.administrator?
  end
end
```

There's a lot more to the grant statement than shown in the above example. For instance, you can have multiple grant statements for the same action. Ultimate permission to perform the action will not be granted unless all grant blocks evaluate to true.

As you can see, Grant is pretty simple to use, but it's not going to do the dirty work for you. It's up to you to make the proper security decisions. Grant's just there to make sure you don't forget.  
