---
layout: post
title:      "From Ajax to Fetch"
date:       2019-10-17 21:57:02 +0000
permalink:  from_ajax_to_fetch
---


Initially I started my JavaScript project following the old requirements of the v7 track. I implemented my GET and POST requests to the server using the jQuery .ajax() method, which in essence initiates an XMLHttpRequest, but the jQuery library provides a cleaner, shorter syntax. Meanwhile, with the transition to the v8 track, the requirement changed to using the Fetch API, which is a more modern version that has several advantages over AJAX.  Both AJAX and Fetch allow only a portion of the webpage to be updated without reloading the whole page.

In the case of my Rails project, once the user goes to the listing of all bookings (index page), clicking to view the details of a bookings leads (acording to RESTful conventions) to /bookings/:id  page. Now, by using asynchronous JavaScript, that functionality can be achieved on the same page without reloading. This can be achiever via AJAX or Fetch. I will explore some of the differences I observed in the way data is returned and parsed between the two approaches.

To start with, a basic .ajax() call to get the user's bookings from the Rails backend can be implemented as follows:

```
$.ajax({
        method: 'GET',
        url: '/users/' + userId +'.json',
        dataType: 'json',
    })
    .done(function(data){
        console.log(data)
    });

```
 The data looks as follows:
 
```
{…}

bookings: (2) […]

0: Object { id: 10, num_adults: 1, num_children: 2, … }

1: {…}

created_at: "2019-10-16T21:37:43.263Z"

cruise: "Dolphin Watch Full Day"

id: 11

num_adults: 2

num_children: 1

__proto__: Object { … }

length: 2

__proto__: Array []

```
 
 From what is logged in the console we can observe that the data is not just a string, it was already parsed by jQuery. We specified the type of data with the dataType: 'json' parameter.
 
 Now, to achieve the same thing using most basic fetch request we can write:
 
```
const url = '/users/' + userId +'.json';

  fetch(url)
    .then(function(res){
       console.log(res);
    })
```

In this case what is logged in the console looks different. We can notice that we get the entire response, including a status code:

```
Response { type: "basic", url: "http://localhost:3000/users/1.json", redirected: false, status: 200, ok: true, statusText: "OK", headers: Headers, bodyUsed: false }

```
So, where is the data in this response? In order to extract the data, we can use the built-in .json() method. However, if we simply try to prnt the data as:

```
fetch(url)
    .then(function(res){
        console.log( res.json());
    })
```
we immediately get not the data, but a promise, i.e. a value that is not available yet, but may be available in the future: 

```
Promise { "pending" }
​
<state>: "pending"
​
__proto__: PromiseProto { … }
```

To handle the promise, we will chain once more the .then() function which can take two arguments - a success and an error handler (but in the default case as mine can be only success).

```
 fetch(url)
    .then(function(res){
        return res.json();
    })
    .then(function(data){
        console.log(data);
    })
```

Now the data printed in the console looks like the one from the .ajax() call:

```
{…}
​
bookings: (2) […]
​​
0: Object { id: 10, num_adults: 1, num_children: 2, … }
​​
1: Object { id: 11, num_adults: 2, num_children: 1, … }
​​
length: 2
​​
__proto__: Array []
​
```
So, the objective of getting the parsed json data via fetch was achieved and from here the data can be processed in the same way as it was with the ajax call.


