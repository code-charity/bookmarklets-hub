# YouTube audio boost

This bookmark adds a slider to the video player (next to volume control) to give additional volume boost to the video player (also boosts ads volume though).

When used, it will ask for the maximum volume boost (default `5`; minimum `2`) for the slider.

> [!NOTE]
>
> Shows an alert box if the video or volume control elements couldn't be found.

## Instructions

Create a new bookmark with any name and use the following text as the URL (copy-paste).

```javascript
javascript:(()=>{"use strict";const m=document.querySelector("div#movie_player>div.html5-video-container>video");const v=document.querySelector("div#movie_player>div.ytp-chrome-bottom>div.ytp-chrome-controls>div.ytp-left-controls>span.ytp-volume-area");if(m==null||v==null)return alert("Couldn't find YouTube player");const x=Number(prompt("Max boost value","5")??5);const a=new AudioContext();const g=new GainNode(a,{gain:1});new MediaElementAudioSourceNode(a,{mediaElement:m}).connect(g);g.connect(a.destination);const l=document.createElement("datalist");l.appendChild(Object.assign(document.createElement("option"),{value:"1",label:"100%"}));const r=Object.assign(document.createElement("input"),{type:"range",min:String(Math.max(0,g.gain.minValue)),max:String(Math.min(Math.max(2,Number.isNaN(x)?5:x),g.gain.maxValue)),value:String(g.gain.value),step:"any",style:"color-scheme:dark;accent-color:#f44;color:#0f0;vertical-align:middle"});r.setAttribute("list",l.id="AudioBoost-notches");r.addEventListener("input",()=>{g.gain.value=r.valueAsNumber;},{passive:true});const c=document.createElement("label");c.title="Audio boost";c.append(r,l);v.insertAdjacentElement("afterend",c);console.debug("Audio boost",g,c);})();
```

The text is minified JavaScript, which executes when the bookmark is clicked (on the current page).

## How it works

1. Creates audio context, media element source (from `div#movie_player>div.html5-video-container>video`), and gain node
2. Creates a slider with a tickmark at value `1` and styling to fit the UI
3. connect audio notes: video → gain → output
4. add elements to DOM (after `div#movie_player>div.ytp-chrome-bottom>div.ytp-chrome-controls>div.ytp-left-controls>span.ytp-volume-area`)
5. logs container element and gain node to console (debug log level)
6. ...
7. profit

---

<details closed><summary>Click to show formatted JavaScript</summary>

```javascript
(() => {
    "use strict";
    const mediaElement = document.querySelector("div#movie_player>div.html5-video-container>video");
    const volumeButton = document.querySelector("div#movie_player>div.ytp-chrome-bottom>div.ytp-chrome-controls>div.ytp-left-controls>span.ytp-volume-area");
    if(mediaElement == null || volumeButton == null) return alert("Couldn't find YouTube player");
    const maxVolume = Number(prompt("Max boost value", "5") ?? 5);
    const audioContext = new AudioContext();
    const gainNode = new GainNode(audioContext, { gain: 1 });
    new MediaElementAudioSourceNode(audioContext, { mediaElement }).connect(gainNode);
    gainNode.connect(audioContext.destination);
    const list = document.createElement("datalist");
    list.appendChild(Object.assign(document.createElement("option"), { value: "1", label: "100%" }));
    const range = Object.assign(document.createElement("input"), {
        type: "range",
        min: String(Math.max(0, gainNode.gain.minValue)),
        max: String(Math.min(Math.max(2, Number.isNaN(maxVolume) ? 5 : maxVolume), gainNode.gain.maxValue)),
        value: String(gainNode.gain.value),
        step: "any",
        style: "color-scheme: dark; accent-color: #f44; color: #0f0; vertical-align: middle"
    });
    range.setAttribute("list", list.id = "AudioBoost-notches");
    range.addEventListener("input", () => { gainNode.gain.value=range.valueAsNumber; }, { passive: true });
    const label = document.createElement("label");
    label.title = "Audio boost";
    label.append(range, list);
    volumeButton.insertAdjacentElement("afterend", label);
    console.debug("Audio boost", gainNode, label);
})();
```

</details>
