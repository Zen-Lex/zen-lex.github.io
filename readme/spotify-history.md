---
layout: post
title: Spotify History
date: 2022-03-21
tags: [JS, HTML-CSS]
splash_img_source: /assets/img/historify-screenshot_wide.png
splash_img_caption: Screenshot of the web app on a laptop.

author: 
  name: Julien
---
# Spotify History

#### A simple client-side web app to display the last songs listen with your Spotify account.

### Two ways to try it :

  1. By logging with your own [Spotify account](https://zen-lex.github.io/historify)
  2. Or with [sample data](https://zen-lex.github.io/historify/sample)

### How does it work :

This web application uses the [Spotify Web API](https://developer.spotify.com/documentation/web-api/quick-start/) to access your account and retrieve data.

First, you'll be ask to connect to your Spotify account and authorize the application to access the following scopes:

- ``user-read-recently-played`` : Access your recently played items.
- ``user-modify-playback-state`` : Control playback on your Spotify clients and Spotify Connect devices.
- ``user-library-read`` : Access your saved content.

Because it's a client-side only application, it implements the [*Implicit Grant Flow*](https://developer.spotify.com/documentation/general/guides/authorization/implicit-grant/) to get an access token. This method provides an unique token useable for one hour only that the application will store as a cookie by using the ``CheckCookie()`` function. Therefore, you will need to reauthorize the application every time the token expires (This can be done automatically by refreshing the page).

Once this is done, the application will call different endpoints to get the recently played tracks and if they are contents or not in your Liked Playlist.
You can interract with each tracks to either, play the song (only if a Spotify client is playing something), like it or add it to queue. You can also click on the track, artist or album name to open the corresponding page in Spotify Web Player.

### Why I've done that :

As always, a new project is a perfect occasion for me to develop and learn new skills. In this case, it was simply to learn how to use JavaScript in a client-side environment and to how to work with an API (making request, parsing data, etc.).

For the API, Spotify is my main way to listen to music in my everyday life and their API is rich and well documented so it was a natural choice for me to work with it.

Have Fun <3 😊

<p align="center"><img class="inbuilt-img" alt="Screenshot of the mobile version" src="/assets/img/historify-screenshot_mobile.jpg" width="243" height="540"></p>
<p class="text-muted caption">Screenshot of the mobile version.</p>
