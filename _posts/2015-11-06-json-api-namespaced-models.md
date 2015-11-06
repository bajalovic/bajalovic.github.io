---
published: true
layout: post
date: "2015-11-06 16:48"
tags: json jsonapi rails namespace
subclass: post
category: bajalovic
---


If you want to have a resource that needs access to a namespaced model, you can overwrite the `resource_type_for(model)` method from `JSONAPI::Resource` module.

For example, I have a model placed at `app/models/weather/alert.rb`, and I created a resource at `app/resources/api/v1/weather_alert_resource.rb`.

Note that resource is not namespaced.

I extended the `resource_type_for(model)` in my `WeatherAlertResource` like this:

~~~ruby
    class Api::V1::WeatherAlertResource < JSONAPI::Resource
      attributes :country, :town, :from, :expires, :description, :alert_type
      model_name 'Weather::Alert'

      class << self
        def resource_type_for(model)
          'api/v1/weather_alert'
        end
      end
    end
~~~

and now I can make a `GET /api/v1/weather_alerts` and everything works fine.
