---
layout: post
title: Environment Specific Default Attachment Options for Paperclip
date: 2010-12-07 09:09:34 -05:00
comments: false
tags: ruby rails
---
[Paperclip](https://github.com/thoughtbot/paperclip) is a popular Ruby file attachment solution for ActiveRecord. Following some basic setup, you can specify an attachment to an ActiveRecord model by calling the has\_attached\_file class method as shown in the example below.

``` ruby
class Photo < ActiveRecord::Base
  has_attached_file :image,
    :styles => { :medium => '350x350>', :thumb => '100x100#' },
    :url    => ':class/:id/:style.:extension',
    :path   => ':rails_root/assets/:class/:id_partition/:style.:extension'
end
```

I love how easy it is to specify an attachment, but I don't like having to specify the :url and :path on all attachments for two reasons:

1. It's not [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself). With the [Paperclip interpolations](https://github.com/thoughtbot/paperclip/wiki/Interpolations) I'm using, the :url and :path are never going to change within an environment no matter how many attachments I have across all my models.
2. Between environments I do need to change the path. In development I keep the attachments in :rails_root/assets but in production I want them somewhere else.

Fortunately, changing the Paperclip attachment defaults per environment is really easy. In your environment config files, do something like the following.

``` ruby
# development.rb (or use environment.rb to setup defaults for all)
Paperclip::Attachment.default_options[:url] = ':class/:id/:style.:extension'
Paperclip::Attachment.default_options[:path] = ':rails_root/assets/:class/:id_partition/:style.:extension'

# production.rb
Paperclip::Attachment.default_options[:url] = ':class/:id/:style.:extension'
Paperclip::Attachment.default_options[:path] = '/usr/local/assets/:class/:id_partition/:style.:extension'
```

Adding those defaults in your environment configuration files means you can keep your models DRY and free of any ugly code used to change options depending on the environment. Isn't this nicer?

``` ruby
class Photo < ActiveRecord::Base
  has_attached_file :image, 
    :styles => { :medium => '350x350>', :thumb => '100x100#' }
end
```
