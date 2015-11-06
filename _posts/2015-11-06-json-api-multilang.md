---
layout: post
title: JSON API Multi Lang Support
date: {}
tags: json jsonapi rails i18n
subclass: "post tag-fiction"
category: bajalovic
published: true
---


I started using `jsonapi-resources` gem in order to create a JSON API according to JSON schema  defined by http://jsonapi.org/

I encountered the issue with multi-lang support. 

In my Ruby on Rails project I am using `globalize` gem for multilang model attribute translations.

I started using `globalize-accessors` gem as well, as they provide getters and setters for translated fields.

This is the JSON response for 

**GET /api/v1/pages/8**

~~~json
{
  "data": {
    "id": "8",
    "type": "pages",
    "links": {
      "self": "/api/v1/pages/8"
    },
    "attributes": {
      "slug": "testing-translations",
      "translations": [
        {
          "locale": "en",
          "title": "Testing Translations",
          "body": "<strong>Lorem</strong> ipsum dolor sit amet"
        },
        {
          "locale": "de",
          "title": "Testing Übersetzungen",
          "body": null
        },
        {
          "locale": "fr",
          "title": "Essais Traductions",
          "body": null
        }
      }
    }
  }
}
~~~

I defined PageResource  as

~~~ruby
attributes :slug, :translations

def translations
    translations = {}
    Page.globalize_locales.each do |locale|
      translations <<  {
          locale: locale,
          title: @model.send("title_#{locale}"),
          body: @model.send("body_#{locale}")
      }
    end
    translations
  end
~~~

This will generate a flatten relationship output. 

In order to update translations in same manner, I had to enable translations through the strong parameters

~~~ruby
def page_params
    params.require(:data).permit(attributes: [:slug, translations: [:locale, :title, :body]])
  end
~~~

As this will call `translations=(data)` on Page instance, I created this method:

~~~ruby
class Page < ActiveRecord::Base
  translates :title, :body
  globalize_accessors

  validates :slug, presence: true

  def translations=(data)
    data.each do |o|
      self.send("title_#{o[:locale]}=", o[:title]) unless o[:title].blank?
      self.send("body_#{o[:locale]}=", o[:body]) unless o[:body].blank?
    end

  end
end

~~~

That's it.

According to the schema, the PATCH (or POST) request would be:

~~~json
{
    "data": {
        "type": "pages",
        "attributes": {
            "slug": "test-ing",
            "translations": [
                {
                    "locale": "en",
                    "title": "Testing Translations",
                    "body": "Lorem ipsum dolor sit amet"
                },
                {
                    "locale": "de",
                    "title": "Testing Übersetzungen",
                    "body": "Einige Blindtext sollte hier gehen"
                },
                {
                    "locale": "fr",
                    "title": "Essais Traductions"
                    "body": "Certains faux texte devrait aller ici"
                }
            ]
        }
    }
}
~~~
