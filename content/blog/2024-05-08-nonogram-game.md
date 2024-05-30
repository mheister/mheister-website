+++
title = "A Nonogram Game Using Yew and SVG"
[taxonomies]
tags = ["programming","rust","wasm","games"]
+++

Warning: js/wasm, bright screen even in dark mode :)

<!-- more -->

Among the abandonded project graveyard on my computer I found this nonogram
game ([›wikipedia](https://en.wikipedia.org/wiki/Nonogram)). It is a WebAssembly-based
single page application using [Yew](https://yew.rs/) that manipulates an SVG element to
render the playing board. I found it funny how Yew as a web app framework is able to
modify the individual elements within the SVG element.

The game allows to set, solve, and share nonograms. In setting mode, left click cells or
drag rectangles to mark cells, right click cells or right-click-drag to unmark. The hints
are generated automatically while setting. In solving mode, left click paints a cell,
either black or red if incorrect. Right click will mark a cell as known-free (gray X).
Known-free cells cannot accidentally be painted. There is no scoring and no indication
when the puzzle is fully solved.

[›Game source on GitHub](https://github.com/mheister/yew-nonograms)

Click <a href="/nonogram_game/" target="_blank">›here</a> if the iframe below does not work (very bright)

{{ nonogram_game() }}
