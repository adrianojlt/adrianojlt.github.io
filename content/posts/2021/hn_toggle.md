+++
title = 'Hacker News Toggle Comments'
date = '2021-06-26'
draft = false
#author = 'adriano'
header_image = "/images/hn_toggle.png"
+++

I like to read [Hacker News](https://news.ycombinator.com/) especially due to the quality of the user comments, the more active comments are those that offer and have more useful information, most of the time we can learn more in the comments section than the story itself.

But, the UI/UX Hacker News comment section doesn't offer much options, we can pretty much only collapse the comments individually. There is no option to collapse all of them at once, and no option either to sort by the most active comments.

To have my minimal needs filled, I just need a way to collapse all the comments at once, after that, it's easier to browse the comment section picking at first the comments with more activity.

## THE SOLUTION

First step, let's do an inspection in the comment collapse button

![inspect image](/images/hn_toggle_inspect.png)


```Javascript
var entries = document.getElementsByClassName('athing comtr');
Object.entries(entries).forEach(e => { 
    try{ toggle( {}, e[1].getAttribute("id") ); } catch {}
});
```

Right?

![arnold](/images/arnold.gif)

Wrong!

![errors](/images/hn_toggle_error.png)

It looks like for each comment there is a call to the server to try to save the comment state. If we manually collapse only one comment we can see a HTTP GET being done to the server, It seems the request is being blocked if they are all made at the same time, a server defense against DOS attacks perhaps. One solution would be to add a timeout, but we will have to wait to have all the comments collapsed. Anyway, we don't need to save the state, we only need to have all the goddammit comments collapsed, so let's inspect where it's being done.

```Javascript
function toggle (ev, id) {

  var tr = $(id), on = !hasClass(tr, 'coll');

  collstate(tr, on);

  (on ? hidekids : showkids)(tr);

  if ($('logout')) {
    new Image().src = 'collapse?id=' + id + (on ? '' : '&un=true');
  }

  ev.stopPropagation();

  return false;
}
```

`new Image()` ???? Where is the need to download an image here? Well!!! guess what! Do you want the simplest way to make an Ajax call to the server? There you have it, just create an image object and set its src attribute to your target endpoint, in this particular case, the endpoint to save the comment state.
We can get rid of that call and the `try` that avoids the exception in the `ev.stopPropagation();`, after that we end up with this solution:

```Javascript
function toggleWithoutRequest (id) {
  var tr = $(id), on = !hasClass(tr, 'coll'); 
  collstate(tr, on);
  on ? hidekids(tr) : showkids(tr);
} 

var mytoggle = async () => {
  var entries = document.getElementsByClassName('athing comtr'); 
  Object.entries(entries).forEach(e => {
    toggleWithoutRequest(e[1].getAttribute("id")); 
  }); 
};
```

Finally, we can wrap this solution in a Javascript self-execution function `function(){}()`, add the JS URL identifier so the browser knows he is dealing with JS: `javascript:` and save it as a [Bookmarklet](https://www.freecodecamp.org/news/what-are-bookmarklets/) favorite:

```Javascript
javascript:(function(){function toggleWithoutRequest (id) {var tr = $(id), on = !hasClass(tr, 'coll'); (on ? addClass : remClass)(tr, %27coll%27); collstate(tr,on); on ? hidekids(tr) : showkids(tr); } var mytoggle = async () => {var entries = document.getElementsByClassName(%27athing comtr%27); Object.entries(entries).forEach(e => {toggleWithoutRequest(e[1].getAttribute("id")); }); }; mytoggle(); })();
```

Now when at a Hacker News page with comments we can collapse them all with a simple click in the Favorite.
