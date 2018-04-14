---
title: Mon fichier trace-agent.log contient l'erreur "empty `service`"
kind: faq
---

Si votre agent ne restitue pas vos traces sur l'interface utilisateur, le premier endroit où rechercher les erreurs est le fichier `trace-agent.log`.

Vous pouvez observer les erreurs suivantes:

```
2017-03-31 08:36:44 ERROR (receiver_logger.go:21) - dropping trace reason: 
invalid span Span[t_id:631844*********,s_id:631844********,p_id:631844*****,ser:,name:<your.span.name>,
res:<your.request.name>]: span.normalize: empty `Service` (debug for more info)
```

### Check that the service was properly defined

Note that this issue is more likely to happen when manual instrumentation is used, especially in languages such as Go. Refer to our documentations for further details: [this page][1] leads you to the specific languages libraries we use.

### My service is defined properly

The issue is probably linked to the actual location of your code instrumentation.

The goal here is to always have a Root span define when a Child span is called when an application is running.

A wrong instrumentation can lead to a dissociation of that Child span to its Rootspan, either at every span call, or in some specific path calls in the application.

Let's say we have a user hitting a web application >> calls a Database.

We could expect the path of action to be this

User hit web application >> Handler function called >> lower level span created (child span ) >> Calling back the Root span.

If the Child span isn't associated directly to the Root span, you could end up with the error described above, especially if the Child service span definition is based on the Parent span definition.

### Nope, my code is properly instrumented

It's time to contact [us][2]!

Send us your flare, and details about the language / library you're using, and a snippet of your code instrumentation.

[1]: /tracing/setup
[2]: /help
