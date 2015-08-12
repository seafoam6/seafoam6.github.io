---
layout: post
title:  "Working with Scanner devices and angularjs"
date:   2015-08-11 18:15:51
categories: angularjs programming
comments: true
---

Recently my work has had me working in a unique, or at least, [underdocumented][swiperepo] set of hardware and software. I've never done any developer blogging, but I want to try to pay it forward for all the blogs that have helped me in the past. 

I recently wrote an angular front end for a large scary [Microsoft Dynamics][dynamics] application. The point of contact for the application to be consumed was a 2014 ipod touch placed in a Captuvo sled. If you're not familiar with captuvo sleds they turn your ipod or iphone into a hand held scanner. Rather than use the ipod on screen keyboard, you hold a button, it scans using the ipod camera and a little grocery store style laser. The value that is scanned is immediately entered into the field. 

The Captuvo sled saves you the time of punching in all those tiny keys with your fat fingers. But you still need to press the 'enter' key, as well as navigate around the page. Luckily there is an application, that is a complete heaven send, the [SwipeTrack Browser][swipetrack]. SwipeTrack browser is a handy wrapper that wraps the default browser and adds a few functions via the API. I REALLY WISH they documented more features. But here are the important ones:

* You can trigger a device sound. There are 3. Alert, error, and success. Unfortunately you cannot just load your own sounds. Which makes zero sense to me. 
* On the iphone you can trigger a vibration and set the duration of the vibration. This kind of feedback is super useful (ipods do not have a vibration mode).
* You can trigger custom delimeter after the scan, such as: space, tab, return, submit. I've found on things like 'return' and 'submit' mileage varies depending on the implementation of your app. Our ASP.NET application behaved differently than our Angular app.
* *you can call javascript functions after a scan*. YES YES YES YES YES. This is so great. It opens up a world of possibilities.

I've built two applications using this devices/software combination. For the simplist interoperability between scanners and applications, I simply have each scanner calling ```scan()```. I define scan() differently in each app, and really you don't want to have to type in a ton of javascript in to a scanner using your fat fingers. 

Having access to call one function and use jquery allows you to find out which input field you're in, pass the value to your javascript, ```focus()``` the next field and so on. You can reduce the amount of keypad touches drastically. 

I use this helpful block to trigger sounds throughout my app 

```
function soundSucces() {window.location = "stBrowser://playSound?sound=success";}
function soundAttention() { window.location = "stBrowser://playSound?sound=attention";}
function soundError() { window.location = "stBrowser://playSound?sound=error";}
```

I wish you could here the ```soundError()``` it sounds like a sad robot. The use of ```window.location``` is how swipeTrack has chosen to trigger sounds. Interesting, eh?

## Capturing values from input fields

I'm going to show how I solved a bug that we encountered testing the scanners. This was for an Angular 1.4 application, and I encourage anyone with a better solution to post it in the comments. 

#### The problem

<p data-height="268" data-theme-id="0" data-slug-hash="rVRggj" data-default-tab="result" data-user="dreamtroit" class='codepen'>See the Pen <a href='http://codepen.io/dreamtroit/pen/rVRggj/'>Updating input values with jquery and angular</a> by Steve Barman (<a href='http://codepen.io/dreamtroit'>@dreamtroit</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

as you can see the scanner doesn't actual update the model. On the first actual keystroke, Angular will update the model. When I submitted the form I would submit **undefined** as the value of the input field. 

#### Solution

It took me a while to figure out because I was so focused on passing the model into the controller on submit. My fix was to call this on submit

```
var scannerField =  angular.element(document.getElementById('scannerField')).val();
```
I then pass around scannerField to any service or model that might need it. Neat-o.



[swiperepo]: https://github.com/swipetrack-solutions/swipetrack-browser-api
[dynamics]: http://www.microsoft.com/en-us/dynamics/default.aspx
[swipetrack]: http://www.swipetrack.com/swipetrack-browser/