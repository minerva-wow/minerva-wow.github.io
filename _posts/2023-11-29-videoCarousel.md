---
title: From Messy to Manageable - How I Improved Apple's Video Carousel Implementation
date: 2023-11-29 03:04:28 +0800
categories: [front-end, react]
tags: [react, gsap, refactor, apple]
---

Hey how's it going? 👋 I wanted to share a little victory from my recent project where I was following along with a tutorial to clone Apple's website. You know that feeling when you're coding along with a video and something just doesn't feel quite right? That's exactly what happened to me.

## The Starting Point
The original implementation worked, but as I was coding it, I kept thinking "this state management feels... messy." Besides, users couldn't jump to a specific video when clicking the indicators. Not great for user experience!

One part that really bugged me was how the video controls were handled. The original code's event handling felt like a game of dominos - one action would trigger another, and sometimes you'd lose track of what caused what. Here's what I mean:

```javascript
// Original state management
const [video, setVideo] = useState({
  isEnd: false, // This felt redundant
  startPlay: false,
  videoId: 0,
  isLastVideo: false,
  isPlaying: false,
});

const [loadedData, setLoadedData] = useState([]); // Separate state for loaded videos

// Original event handling - spot the chain reaction!
const handleProcess = (type, i) => {
  switch (type) {
    case "video-end":
      setVideo((pre) => ({ ...pre, isEnd: true, videoId: i + 1 }));
      break;
    case "video-last":
      setVideo((pre) => ({ ...pre, isLastVideo: true }));
      break;
    case "video-reset":
      setVideo((pre) => ({ ...pre, videoId: 0, isLastVideo: false }));
      break;
    case "pause":
      setVideo((pre) => ({ ...pre, isPlaying: !pre.isPlaying }));
      break;
    case "play":
      setVideo((pre) => ({ ...pre, isPlaying: !pre.isPlaying }));
      break;
  }
};

// And then in the UI...
<img
  onClick={
    isLastVideo
      ? () => handleProcess("video-reset")
      : !isPlaying
      ? () => handleProcess("play")
      : () => handleProcess("pause")
  }
/>;
```

I simplified this into something more straightforward:

```js
const [state, setState] = useState({
  videoId: 0,
  isPlaying: false,
  startPlay: false,
  isLastVideo: false,
  loadedVideos: [], // Brought loadedVideos into main state
});

// New and improved control flow
const handleVideo = (type, i) => {
  switch (type) {
    case "select": // New! Direct video selection
      setState(prev => ({ ...prev, videoId: i, isPlaying: true, isLastVideo: false }));
      break;
    case "toggle": // Combined play/pause into one action
      setState(prev => ({ ...prev, isPlaying: !prev.isPlaying }));
      break;
    case "end":   // More intuitive handling of video end
      if (i === hightlightsSlides.length - 1) {
        setState(prev => ({ ...prev, isLastVideo: true, isPlaying: false }));
      } else {
        setState(prev => ({ ...prev, videoId: i + 1 }));
      }
      break;
    case "reset":
      videoRef.current[videoId].currentTime = 0;
      setState(prev => ({ ...prev, videoId: 0, isLastVideo: false, isPlaying: true }));
      break;
  }
};

// Clean UI implementation
<img
  src={isLastVideo ? replayImg : !isPlaying ? playImg : pauseImg}
  onClick={() => handleVideo(isLastVideo ? "reset" : "toggle")}
/>
```

## Tackling the Progress Bar Chaos

The progress bar was my next target. Looking at the original code, I felt a bit overwhelmed - there was this massive useEffect hook handling all the animation logic. It was like trying to juggle while riding a unicycle! 😅


```js
// The original progress bar beast
useEffect(() => {
  let currentProgress = 0;
  let span = videoSpanRef.current;

  if (span[videoId]) {
    // animation to move the indicator
    let anim = gsap.to(span[videoId], {
      onUpdate: () => {
        // get the progress of the video
        const progress = Math.ceil(anim.progress() * 100);

        if (progress != currentProgress) {
          currentProgress = progress;

          // set the width of the progress bar
          gsap.to(videoDivRef.current[videoId], {
            width:
              window.innerWidth < 760
                ? "10vw" // mobile
                : window.innerWidth < 1200
                ? "10vw" // tablet
                : "4vw", // laptop
          });

          // set the background color of the progress bar
          gsap.to(span[videoId], {
            width: `${currentProgress}%`,
            backgroundColor: "white",
          });
        }
      },

      // when the video is ended, replace the progress bar with the indicator and change the background color
      onComplete: () => {
        if (isPlaying) {
          gsap.to(videoDivRef.current[videoId], {
            width: "12px",
          });
          gsap.to(span[videoId], {
            backgroundColor: "#afafaf",
          });
        }
      },
    });

    if (videoId == 0) {
      anim.restart();
    }

    // update the progress bar
    const animUpdate = () => {
      anim.progress(
        videoRef.current[videoId].currentTime /
          hightlightsSlides[videoId].videoDuration
      );
    };

    if (isPlaying) {
      // ticker to update the progress bar
      gsap.ticker.add(animUpdate);
    } else {
      // remove the ticker when the video is paused (progress bar is stopped)
      gsap.ticker.remove(animUpdate);
    }
  }
}, [videoId, startPlay]);
```

So, I took a deep breath and broke it down into bite-sized pieces:

```js
// Ahh, much cleaner now! No more nested animation callbacks.
const updateProgress = () => {
  const video = videoRef.current[videoId];
  const progressBar = progressRef.current[videoId];
  const progressBarContainer = progressBarRef.current[videoId];

  if (video && progressBar && progressBarContainer && isPlaying) {
    const progress = (video.currentTime / video.duration) * 100;

    gsap.to(progressBarContainer, {
      width:
        window.innerWidth < 760
          ? "10vw"
          : window.innerWidth < 1200
          ? "10vw"
          : "4vw",
    });

    gsap.to(progressBar, {
      width: `${progress}%`,
      backgroundColor: "white",
    });
  }
};

// The useEffect is now focused solely on managing the animation ticker
useEffect(() => {
  if (isPlaying) {
    const updateTicker = () => updateProgress();
    gsap.ticker.add(updateTicker);
    return () => gsap.ticker.remove(updateTicker);
  }
}, [videoId, isPlaying]);

// Separate reset logic, now called explicitly when needed
const resetProgressBar = (index) => {
  if (progressRef.current[index] && progressBarRef.current[index]) {
    gsap.to(progressBarRef.current[index], {
      width: "12px",
      duration: 1,
      ease: "power2.out",
    });
    gsap.to(progressRef.current[index], {
      width: "100%",
      backgroundColor: "#afafaf",
    });
  }
};
```

Like a messy drawer got some organizers, now everything has its place. Need to update the progress? There's a function for that. Need to reset it? There's a clean function for that too. No more digging through nested callbacks or trying to understand complex animation logic!

## Last but not Least

Another small but satisfying improvement was how the videos themselves are handled when switching between them:

```js
// Original version: If you clicked another indicator while the video was unfinished, 
// you would see two progress bars growing simultaneously and the playback sequence 
// completely out of order!
useEffect(() => {
  if (loadedData.length > 3) {
    if (!isPlaying) {
      videoRef.current[videoId].pause();
    } else {
      startPlay && videoRef.current[videoId].play();
    }
  }
}, [startPlay, videoId, isPlaying, loadedData]);

// My version: traverse the videos to give them the right behavior, no one left behind.
useEffect(() => {
  if (loadedVideos.length >= videoRef.current.length) {
    videoRef.current.forEach((video, i) => {
      if (i === videoId && startPlay) {
        isPlaying ? video.play() : video.pause();
      } else {
        video.pause();
        video.currentTime = 0;
        resetProgressBar(i);
      }
    });
  }
}, [startPlay, videoId, isPlaying, loadedVideos]);

```

Now when you switch videos, everything gets reset properly - the old video stops and rewinds, the progress bar resets, and the new video starts playing. No more weird states where multiple videos might be playing at once! 🎉

## A Little Change Goes a Long Way

Never thought tweaking a few state variables and reshuffling some functions could make such a difference. Goes to show that even experienced developers can overcomplicate things. So don't be afraid to question and remember: KISS (Keep It Simple, Stupid)! 🌟
