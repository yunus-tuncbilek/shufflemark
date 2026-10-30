# Shufflemark

<img src="icon.svg" alt="Shufflemark logo" width="64">

A bookmark that plays a random video from a YouTube playlist. Free and open
source.

## How it works

1. In YouTube, open your playlist and copy its link from the address bar.
2. Paste the link into the box on the Shufflemark page. A blue box shows your
   personal Shufflemark link.
3. Whenever you open the link, a random video from your playlist starts.

## Technical decisions and why
1. An API key is required.
   
   Opening a YouTube URL with a playlist id and a random index doesn't launch 
   the video of the given index. For instance, something like this doesn't work:
   
   ```
   https://www.youtube.com/watch?list=<playlist id>&index=<n>
   ```
   
   Without a `v=` (video ID) parameter, YouTube shows a blank player with the
   message "YouTube is not currently available on this device". This was tested by
   hand in a browser on 2026-10-18. So the page needs real
   video IDs, which it gets from the YouTube Data API.

   The deployed page contains the key, since the browser calls the API directly.
   This is normal for browser API keys: the website restriction stops other sites
   from using it. The free quota is 10,000 units a day and a playlist costs one
   unit per 50 videos, so with the 24-hour cache it goes a long way.

2. Mobile gets a button.
   
   On iOS, a `youtube.com` link opens the YouTube app through Apple's *universal
   links*, but only when the user started the navigation by tapping something. A
   redirect the page does by itself stays in Safari. Android's app links behave
   similarly. So on phones and tablets the page waits for a tap on
   **Open in YouTube** instead of redirecting.

3. Temporary caching of video IDs is a good idea.
   
   The video IDs are cached in `localStorage` for 24 hours, so most
   clicks don't call the API. Private and deleted videos are skipped.
   Temporary over permanent because the playlists may change.

4. Deployment over a static page to remove API key from repo.

   The page is deployed by
   [`.github/workflows/pages.yml`](.github/workflows/pages.yml), which replaces
   the `__YT_API_KEY__` placeholder in `index.html` with the key from **YouTube Data API v3**. 
   So the key is never in the repo, even though it's on the webpage.
