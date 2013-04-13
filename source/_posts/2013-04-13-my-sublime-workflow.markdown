---
layout: post
title: "My Sublime Workflow"
date: 2013-04-13 21:43
comments: true
categories: sublime-text, configuration
---

The settings I use for [Sublime Text 2](http://www.sublimetext.com/):

```json

{
    "color_scheme": "Packages/User/Tomorrow-Night.tmTheme",
    "detect_slow_plugins": false,
    "draw_white_space": "all",
    "find_selected_text": true,
    "fold_buttons": false,
    "folder_exclude_patterns":
    [
        ".svn",
        ".git",
        ".hg",
        "CVS",
        "_build",
        "dist",
        "build",
        "site"
    ],
    "font_face": "Ubuntu Mono",
    "font_options":
    [
        "subpixel_antialias"
    ],
    "font_size": 13,
    "highlight_line": true,
    "ignored_packages":
    [
        "Vintage"
    ],
    "rulers":
    [
        80,
        100
    ],
    "soda_classic_tabs": true,
    "theme": "Soda Dark.sublime-theme",
    "translate_tabs_to_spaces": true,
    "trim_trailing_white_space_on_save": true
}

```

###Plugins

*   Emmet-Sublime
*   SublimeLinter
*   SublimeREPL
*   SideBarEnhancements
*   Markdown Preview
*   Git

###Others

* [Tomorrow-Night color scheme](https://github.com/chriskempson/tomorrow-theme)
* [Soda Dark theme](https://github.com/buymeasoda/soda-theme/)
