---
title: "Automate Browser Using Tampermonkey and ChatGPT"
datePublished: Sun May 04 2025 08:09:02 GMT+0000 (Coordinated Universal Time)
cuid: cma9dejh3000009jv5gyngmb2
slug: automate-browser-using-tampermonkey-and-chatgpt
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746344504266/0f1a0440-23da-4466-9fe9-d1742de564ac.png
tags: ai, javascript, technology, browsers, automation, userscript, tampermonkey, browser-automation, chatgpt, violentmonkey

---

## Introduction: Why bother tweaking the browser?

I’ve automated back-end stuff, infra, even my WhatsApp messages (coming in a post soon👁️👁️…)  
But the front-end of my day (Chrome / Edge) stayed painfully manual.

Yet that’s where I:

* open the **same dashboards** 20× a day,
    
* poke the **same buttons** (download / export / filter),
    
* stare at UIs that are *almost* perfect — but not quite *BosTheCoder-perfect*.
    

Real power shows up when either of these is true:

1. You visit one page **all the time** and want it to fit your workflow.
    
2. You need to repeat the **same action on many pages** (think coupon finders, “save-to-Pocket”, [Readwise-highlighter](https://chromewebstore.google.com/detail/readwise-highlighter/jjhefcfhmnkfeepcpnilbbkaadhngkbi?hl=en), etc.).
    

---

## Potential use-cases (a few that keep me up at night)

* **Instant download & sideload**
    
    * Lets say a freind recommends you a book you should read by sending an amazon link.
        
    * You open it up and take a look and it actually looks 🔥so you want to save it so you can read later
        
    * Imaging in one click you could:
        
        * look up ISBN → queue in [StoryGraph](https://app.thestorygraph.com/) / [Goodreads](https://www.goodreads.com/)
            
        * fetch from *Library Genesis* → add to [Calibre](https://calibre-ebook.com/)
            
        * shoot the EPUB to Kindle → sync all devices.  
            That’s like 7 clicks → **1**.
            
* **Auto-file receipts**
    
    * scrape order confirmation pages and dump PDFs straight into my “Receipts → YYYY/MM” folder on OneDrive.
        
* **Hide spoilers**
    
    * every sports page that mentions Arsenal’s score before I’ve watched the game? Insta-blur.
        

Once you think in “tiny browser robots”, you’ll spot ideas everywhere.

---

## “Do I really have to build a Chrome extension?”

Nope.  
The lazy-genius route is a **userscript manager**.

Some of the most used ones today can be seen in the table below:

| Manager | Chrome / Edge | Firefox | OSS? | Notes |
| --- | --- | --- | --- | --- |
| [Tampermonkey](https://tampermonkey.net/) | ✅ | ✅ | ❌ | Slick UI, massive user base |
| [Violentmonkey](https://violentmonkey.github.io/) | ✅ | ✅ | **✅** | Open-source, what I use |
| [Greasemonkey](https://addons.mozilla.org/en-US/firefox/addon/greasemonkey/) | — | ✅ | ✅ | OG, Firefox only |

A userscript is just a `.user.js` file that the manager injects at page-load. Pure JavaScript.  
The beauty of which is that this means **ChatGPT can write it for you** in one shot pretty easily.

And if you use a good *reasoning* model like **o3** it seems to get to the solution pretty quickly as long as you can fit the source for the web page into your prompt (more on this later 🥲)

---

## Concrete example — NeetCode blind-practice mode 🎯

I grind coding interviews on [NeetCode.io](https://neetcode.io/), but:

* The list shows the **difficulty** (Easy / Medium / Hard).
    
* Categories shout **“Graphs”, “DP”, “LinkedList”**.
    

Those are hints — I wanted zero signals. Just a shuffled list where every problem is a surprise.

In my head an easy solution to this would be to:

* **Hide** all category headers.
    
* **Hide** the Difficulty column.
    
* **Merge + shuffle** every table into one mega-table.
    

*(Basically, turn the site into Leetroulette.)*

### Using ChatGPT to crank out the script

The prompt itself was relatively simple, it basically was just:

> “Generate a Tampermonkey userscript that hides `<p class='header'>…</p>`, removes the Difficulty column, merges all `<app-table>` elements, and shuffles the rows. Must work on a single-page Angular site. See page source below: `<page source>`”

I did however run into a few issues…

| Issue | What happened | Fix |
| --- | --- | --- |
| **Context was too big** | Copy-pasting the whole DOM blew GPT’s token limit. | I had to trim the html down of non-useful info to get it to its bare bones. I used the help of `gemini 2.5 pro` to do this as it had a large context window. |
| **Worked in the console, broke in Tampermonkey** | NeetCode is an [SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA), which meant that the script ran **before** Angular rendered. | Added `@run-at document-end`, a `MutationObserver`, debounced reruns, and an `history.pushState` hook. |
| **Browser constantly dying on me** | My observer fired on *every* DOM twitch → infinite loop. | One-shot flag + 60 ms debounce solved it. |

### Final script

You can grab the final script [here](https://github.com/BosTheCoder/scripts/tree/main/scripts/coding/neetcode_blind_practice) and follow the instructions to use in TamperMonkey.  
To use it navigate to [NeetCode’s Problem list](https://neetcode.io/practice). :)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746345816271/74430dde-fbbd-4945-9804-d7a93bc42882.png align="center")

## Take-aways

1. **Userscripts give 80 % of a browser-extension’s value** for 5 % of the effort.
    
2. ChatGPT (o3) + a crisp prompt = rapid DOM surgery.
    
3. Always remember SPAs mutate the page **after** load — listen for changes or you’ll rage-debug a “working” script.