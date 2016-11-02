Every time you, as a developer, receive an email with a job offer you see 
things like:

> Technologies you will be working with: CSS3, Angular/React, jQuery, HTML5

This is only an example, and the strange thing for this example is the 
combination of Angular/React and jQuery.

## A required introduction to the problem.

### Framework + Library. Who, when and why.

Angular and React are frameworks. In general, a framework is a real or 
conceptual structure intended to serve as a support or guide for the building 
of something that expands the structure into something useful.

jQuery is a library is a set of functinalities you can use in another system.

### Combining for a desaster

The main problem to combine a framework and a library that doesnt belong to 
that framework creates incompatibilities or issues with responsabilities.

For example, if the framwork has support for http get request and the library 
too, which one should be use? what if we use the framework in some cases and 
the library others, what rule defines which one to use and when?.

### Only one to rule them all

If anyone tells you to have jQuery in your project because you can not do 
something with AngularJS (I assume is the same with React) and you can do it 
with jQuery. Please check that's person knowlege. 

Is almost sure you'll start to have more problems than solucitions if you
mantain that combination.

## AngularJS, React, Ember, etc are only a replacement of jQuery.

Yes and no, the quick answer is that this frameworks do all what jQuery does.

The "no" part of the replacement, points to understand the scope of a framwork
like this. If you only use this frameworks like a replacement you're missing 
most of the power of it.

This frameworks provide a complete way of think you applications, you're going
to structure your applications using this tools to create code you can mantain
in the future.

If you use a library like jQuery, you as a developer, are responsable to design
, implement, and follow internal -and non general- rules of what, when and how 
to do something.

If you use a framework, the framework itself works as a guide -and I put the 
word guide on purpose, becuase as any framwork, you can do wherever you want
breaking the framwork guidelines- about how you do things.

## A big introduction to get the point

Yes, if you still reading this, I've wrote a lot to put you on the same page on
what I want to explain and what is the new piece in the puzzle of system
development I see.

### Is not BackEnd, is not FrontEnd

If we this in terms of the MVC, the backend is the Model, the frontend is the 
view, but how do you call the controller?

If think in terms of the dispatcher pattern, the backend is the actions/store, 
the view is the frontend and whats the dispatcher itself?

### Meet the MiddleEnd

Yes, is a bad name. If someone call me "hey! middleend, you broke last 
deployment" is not going to work.

But the concept is there, the middleend is a role of someone who knows about
frameworks as we described before. 

When a company or a development team starts using this kind of frameworks we 
may have two important problems: 1) not fully understand whats the scope for 
that framework, what is should be done using the framework, how a framework 
like this can affect all the system. 2) Mantain coexisting libraries and 
frameworks for the same tasks.

Both problems can be solve with the correct expertice and not to address this 
problems at the same time when we introduce this new frameworks it may end in a
wrong perception of what we can solve with this new tools.

### More than frameworks

A few years ago we faced a big changes to development systems. Some of us can 
remember how it was create application to consume APIs, but this was done with 
languages like PHP for example and using jQuery in the frontend for some ajax
calls.

Now all is different, instroduction new frameworks to consume many asyncronicus
request, different services, different APIs is a complete new game. We fece the 
challange to organize and mantain large systems, during the development moment 
of the first version and after some time when the original scope of 
applications changes, we need to be able of apply those changes to our systems.

This is the challenge of the new developers, this is one of the most important
roles of a middleend developer. Be ready to work with the architecture of what
is going ti be delivered to the customers today and tomorrow and dont die in 
the "middle".