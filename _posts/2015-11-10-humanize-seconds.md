---
published: false
layout: post
date: "2015-11-10 10:11"
tags: rails helper
subclass: post
category: bajalovic
---

Humanize seconds helper

~~~ruby
    def humanize_seconds secs, full = false
      [[60, :seconds], [60, :minutes], [24, :hours], [1000, :days]].map { |count, name|
        if secs > 0
          secs, n = secs.divmod(count)
          trans = name
          trans = full ? " #{I18n.t("global.time.#{name}")}" : I18n.t("global.time.short.#{name}")
          "#{n.to_i}#{trans}"
        end
      }.compact.reverse.join(' ')
    end
~~~

~~~
   en:
     time:
      seconds: "seconds"
      minutes: "minutes"
      hours: "hours"
      days: "days"
      short:
        seconds: "s"
        minutes: "m"
        hours: "h"
        days: "d"
~~~

Use it like this 

~~~ruby
<%= humanize 343 %>
~~~

and you should get `5m 43s`. Or, 

~~~ruby
<%= humanize 343, true %>
~~~

and you should get `5 minutes 43 seconds`.

Enjoy.