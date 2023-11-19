# YouTube popup player

This bookmark lets you open any YouTube video or playlist in a new popup-like window that uses YouTube's embed-URL to show no ads.

>
> [!NOTE]\
> If no video or playlist is found, an alert box will show.
>

## Instructions

Create a new bookmark with any name and use the following text as the URL (copy-paste).

```JavaScript
javascript:(()=>{"use strict";const t=document.body.querySelector("video.video-stream.html5-main-video"),e=window.location.search.match(/[?&]v=([^&]+)/)?.[1],o=window.location.search.match(/[?&]list=([^&]+)/)?.[1];if(!e&&!o)return alert("No video and/or playlist found");const i=t?(()=>{t.pause();const{left:i,top:n,width:a,height:r}=t.getBoundingClientRect();return window.open(`${window.location.protocol}//www.youtube.com/embed/${e??"videoseries"}?autoplay=1&start=${Math.trunc(t.currentTime)}${o?`&list=${o}`:""}`,"_blank",`menubar=0,status=0,titlebar=0,top=${window.screenTop+n},left=${window.screenLeft+i},width=${Math.max(100,a)},height=${Math.max(100,r)}`)})():window.open(`${window.location.protocol}//www.youtube.com/embed/${e??"videoseries"}?autoplay=1${o?`&list=${o}`:""}`,"_blank",`menubar=0,status=0,titlebar=0,top=${window.screenTop},left=${window.screenLeft},width=${Math.max(100,window.innerWidth)},height=${Math.max(100,window.innerHeight)}`);i&&e&&o&&i.addEventListener("load",(()=>{const t=i.document.querySelector("div#player div.ytp-title-text>a[href]");t&&t.href.match(/[?&]v=([^&]+)/)?.[1]!==e&&(i.location.search=i.location.search.replace(/(\?)list=[^&]+&|&list=[^&]+/,"$1"))}),{passive:!0,once:!0})})();
```

The text is minified JavaScript, which executes when the bookmark is clicked (on the current page).

## How it works

It reads the `v` (video ID) and `list` (playlist ID) attributes from the URL, so it _should_ also work on sites like https://piped.video ([GitHub link](https://github.com/TeamPiped/Piped "GitHub: TeamPiped/Piped")).

Also checks the time of the YouTube video player (first `video` element, with classes `video-stream` and `html5-main-video`, found) to start the video in the popup at that time (and also pauses the video player if it's still playing).

>
> [!NOTE]\
> There is an edge case when opening a video with a playlist, where the video is further into the playlist (embed-URL only shows the first 200 entries of a playlist),
> in such a case (when it gets detected) it reloads the page without the playlist to still show the correct video (it might take a second or two to detect and reload the page).
>

---

<details closed><summary>Click to show formatted JavaScript</summary>

```JavaScript
(() => { // Open YouTube video or playlist as embed in a popup window (reads `v` and `list` URL parameters and gets the time from the first `video.video-stream.html5-main-video` element found)
    "use strict";
    const player = document.body.querySelector("video.video-stream.html5-main-video"),
        video = window.location.search.match(/[?&]v=([^&]+)/)?.[1],
        list = window.location.search.match(/[?&]list=([^&]+)/)?.[1];
    if(!video && !list) return alert("No video and/or playlist found");
    const popup = player ? (() => {
            "use strict";
            player.pause();
            const { left, top, width, height } = player.getBoundingClientRect();
            return window.open(
                `${ window.location.protocol }//www.youtube.com/embed/${ video ?? "videoseries" }?autoplay=1&start=${ Math.trunc(player.currentTime) }${ list ? `&list=${ list }` : "" }`,
                "_blank",
                `menubar=0,status=0,titlebar=0,top=${ window.screenTop + top },left=${ window.screenLeft + left },width=${ Math.max(100, width) },height=${ Math.max(100, height) }`
            );
        })() : window.open(
            `${ window.location.protocol }//www.youtube.com/embed/${ video ?? "videoseries" }?autoplay=1${ list ? `&list=${ list }` : "" }`,
            "_blank",
            `menubar=0,status=0,titlebar=0,top=${ window.screenTop },left=${ window.screenLeft },width=${ Math.max(100, window.innerWidth) },height=${ Math.max(100, window.innerHeight) }`
        );
    if(popup && video && list){
        popup.addEventListener("load", () => {
            "use strict";
            const title = popup.document.querySelector("div#player div.ytp-title-text>a[href]");
            if(title && title.href.match(/[?&]v=([^&]+)/)?.[1] !== video) popup.location.search = popup.location.search.replace(/(\?)list=[^&]+&|&list=[^&]+/, "$1");
        }, { passive:true, once:true });
    }
})();
```

</details>
