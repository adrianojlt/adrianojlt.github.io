+++
title = 'Claude Code and Sudoku'
date = '2026-02-04'
draft = false
tags = ['dev']
#author = 'adriano'
header_image = "/images/claude_code.png"
+++


## Claude Code
One of my first university projects was implementing a Sudoku solver in C. It worked for some puzzles, but it couldn’t solve all of them, especially the hard ones. I remember reaching a point where the only way forward felt like brute force: trying different numbers and hoping something would stick.

Fast forward to today: I haven’t touched C in years. While installing Claude Code to give it a try, I remembered that old Sudoku program and decided to use it as my first interaction with Claude Code starting from my original C code.

Using the `/init` command, Claude Code analyzed the project and created a [CLAUDE.md](https://github.com/adrianojlt/sdk/blob/master/CLAUDE.md) confirming what I suspected, but it also gave the missing piece a proper name: [backtracking](https://medium.com/@andreaiacono/backtracking-explained-7450d6ef9e1a).

`"Limitation: No backtracking — cannot solve puzzles that require guessing/search."`

The first step was [fixing a few minor bugs and asking Claude to add support for importing puzzles from files](https://github.com/adrianojlt/sdk/commit/ee5e3e37b54f3744a32ea5735d5b7d7fc5b64b40) (yes! originally I had to type every number manually). It nailed that on the first try: it installed the required dependencies, compiled the code, and even tested it with real Sudoku puzzles.

Next, [I asked to implement the logic needed to solve harder puzzles](https://github.com/adrianojlt/sdk/commit/cebd2c17b101fa3d4d671713374c9e48c53cd54a). That’s where it went down a path that took around 10 minutes. To be honest, I didn’t pay close attention to what it was doing I mostly answered “yes” to its prompts.

![screen claude](/images/screen_claude.png)

What stood out to me was how human the process looked: it introduced bugs, fixed them, debugged the program, and ran a few tests. Still, it felt like it took too long for the kind of change I was asking for. My guess is that starting from an existing codebase (especially one that wasn’t designed with backtracking in mind) made the job harder than it needed to be. This is the sort of thing that could probably be done faster by starting from a known algorithm or even by asking an LLM to generate a clean implementation from scratch in a matter of seconds.

While it's still impressive, it highlights something important about "vibe coding", the practice of letting an AI drive while you mostly say yes. It works, but it's easy to drift down a suboptimal path without realizing it. A senior developer with experience in Claude Code watching the same session would likely have interrupted earlier: "Don't try to add backtracking onto this structure, refactor first" or "Just start fresh with a known algorithm." That instinct to recognize a bad path before you're deep into it doesn't come from the tool. It comes from experience. So my take on the vibe coding hype: The potential is real, but so are the risks. The tool amplifies what you bring to it. If you know enough to steer, you'll get somewhere useful faster. If you don't, you might spend ten minutes watching an AI debug itself in circles, and not even notice anything went wrong.





