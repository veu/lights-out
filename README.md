# Lights Out

Play the game [Lights Out](https://en.wikipedia.org/wiki/Lights_Out_(game)) in just 235b of (convoluted) HTML and JavaScript.

* [Play the game](http://lights-out.monometric.net)
* [See the source](https://github.com/veu/lights-out/blob/main/index.html)

## How to play

Clicking a light toggles it and its neighbors from on to off and vice versa. The goal is to switch off all lights.

## Features

* Loads the initial state directly from the URL. [Example 1](http://lights-out.monometric.net/?0010001010100010101000100) [Example 2](http://lights-out.monometric.net/?1011100011101011100011101) [Example 3](http://lights-out.monometric.net/?1011101011101011101011101)
* Plays fancy two frame animation when you‘ve won the game.
* Let‘s you undo any move by pressing your browser‘s back button.

## Acknowledgements

Thanks to doughno, katkip, and xen from the [dwitter.net](http://dwitter.net/) [discord](https://discord.gg/emHe6cP) their suggestions and improvements.

## Commented code

To keep the size low the game relies on existing browser functionality.
There‘s no input handler or other dynamic elements.
Just a link to the next state for each of the lights.

```
<body
  onload="                   // We need to bootstrap our code and `onload` is the shortest way.
    for(                     // The outer loop generates the link for each light.
      s=i=>
        location.search[i+1] // Look up the current state from the query or use default state.
        ||i%2,               // This works because the left side of `||` is only falsy if the query is empty.
      C=i=25;                // `C` is used to check the win condition.
      i--;                   // `i` is used to enumerate the lights.
      Z.innerHTML +=         // Append to the body directly since we only update the state once.
        `<a href=?${t}>`+    // Every light links to the next state via query.
        '⚪⚫'[v=s(i)]+      // Render on/off state. Symbols have to be 1 JS character, e.g. 💡 won‘t work.
        (i%5?'':'<br>'),     // Add a break after every 5th light. This works because we‘re iterating backwards.
      C-=v                   // Count lights that are on to detect win state.
    )
      for(                   // The inner loop generates the query with the next state.
        t='',                // `t` is used to store the next state.
        j=25;                // `j` is used to enumerate lights in next state.
        j--;
      )
        t=(
          [                  // Compare position of displayed light (`i`) and next state light (`j`).
            -5,              // Is `i` the top neighbor?
            j%5&&-1,         // Is `i` the left neighbor? Ignore if `j` is at the left border.
            0,               // Is `i` same as `j`?
            i%5&&1,          // Is `i` the right neighbor? Ignore if `i` is at the left border.
            5                // Is `i` the bottom neighbor?
          ].includes(i-j)    // Get relative position.
          ^s(j)              // Use current state or opposite if `j` is adjacent to clicked light
        )+t;                 // Prepend state for current light because we‘re iterating backwards.
        C?0:Z.bgColor=0      // Set black background if all lights are off. Use property from the olden times.
  "
  id=Z                       // Give the body a shorter identifier than `document.body`.
  style=font:8em/1'          // Make the font bigger but reduce line height to get a square.
>
```
