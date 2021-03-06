---
title: "How to use your Garmin watch to tell your team you're going for a run"
post_date: 2020-01-08
layout: single
permalink: /api-call-garmin-watch/
categories: r-blog-en
output: jekyllthat::jekylldown
excerpt_separator: <!--more-->
---

Building an API in NodeJS and R to send message to Slack from your
Garmin watch.

## Why on earth

ThinkR is a remote company, meaning that we all work from our home. On
top of other cool things about remote work, this allows me to skip my
lunch break and take a one hour break in the middle of the afternoon for
sport. And I usually go for a run around 2 or 3 pm, but that moment in
the day is not exactly the same every day. And most of the time, I
forget to tell everyone that I’m leaving the office. I recently joked
that my 2020 resolution was that I’ll be more strict about telling when
I arrive and leave the “office”.

I was sure it can be done straight from my watch. And guess what, it
can\!

## The Slack Part

The slack API is pretty amazing and allows you to use a personal web
hook and a curl call to send messages to a selected channel on Slack.

Note: there are several packages in R that can be used to send messages
to Slack, for example `{slackr}`: <https://github.com/hrbrmstr/slackr> &
`{slackteams}`: <https://github.com/yonicd/slackteams>. But as I just
wanted to make a simple, unique call, it was more straightforward to
write it directly.

So:

  - Go to `https://api.slack.com/`

  - Click on *Start Building*

  - Add an app name and add it to a workspace

<div data-align="center">

<img src = "/assets/img/slackapi1.png">

</div>

  - Add a new “Incoming Webhooks”, and select the Channel to post in

<div data-align="center">

<img src = "/assets/img/slackapi2.png">

</div>

And Tadaa 🎉 you now have a curl call that looks like this:

    curl -X POST -H 'Content-type: application/json' 
      --data '{"text":"Hello, World!"}' 
      https://hooks.slack.com/services/YOUR/WEBHOOK/URL/HERE

Now time to turn this into an API.

## Node API

Here is a very simple API built in NodeJS:

``` javascript
const express = require('express')
const app = express()

app.get('/', function (req, res) {
  const request = require('request');
  
  const options = {
    url: 'https://hooks.slack.com/services/YOUR/WEBHOOK/URL/HERE',
    json: true,
    body: {
      text: "I'm off for a run!"
    }
  };
  
  request.post(options);
  
  res.send('OK')
})

app.listen(9999, function () {
  console.log('API listening on port 9999!')
})
```

## R API

And with R:

``` r
library(plumber)

#* @get /
function() {
  httr::POST(
    url = 'https://hooks.slack.com/services/YOUR/WEBHOOK/URL/HERE', 
    body = list(
      text  = "I'm off for a run!"
    ), 
    encode = "json"
  )
  
}
```

## Adding this to the watch

I discovered that Garmin has a widget called “API calls”, that let you
enter an API endpoint, and the API call is done from the
watch.

<https://apps.garmin.com/en-US/apps/ac9a81ab-a52d-41b3-8c14-940a9de37544>

<div data-align="center">

<blockquote class="twitter-tweet">

<p lang="en" dir="ltr">

I just discovered that I can send API calls from my Garmin watch and I'm
very excited about this and that also made me realized that I'm
definitely a big nerd.
<a href="https://t.co/2h4fk8tCnf">pic.twitter.com/2h4fk8tCnf</a>

</p>

— Colin Fay 🤘 (@\_ColinFay)
<a href="https://twitter.com/_ColinFay/status/1214556262660071427?ref_src=twsrc%5Etfw">January
7,
2020</a>

</blockquote>

<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

</div>

So here it is, I’ve got a Widget on my watch that I can use to send
message on Slack 🎉

<div data-align="center">

<blockquote class="twitter-tweet">

<p lang="en" dir="ltr">

You know you're a nerd when you deploy an API that posts to Slack from
your Garmin watch so that you can tell your team that you're going for a
run instead of just… you know… typing it.
<a href="https://t.co/f2cwn1pxd7">pic.twitter.com/f2cwn1pxd7</a>

</p>

— Colin Fay 🤘 (@\_ColinFay)
<a href="https://twitter.com/_ColinFay/status/1214655430951092224?ref_src=twsrc%5Etfw">January
7,
2020</a>

</blockquote>

<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

</div>
