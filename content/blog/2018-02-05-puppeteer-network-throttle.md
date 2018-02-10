---
date: 2018-02-05T22:00:00+03:00
lastmod: 2018-02-05T22:00:00+03:00
title: Network throttling in Puppeteer
authors: ["fdalvi"]
categories:
  - techtips
tags:
  - chrome
  - webapps
  - testing
  - network
  - puppeteer
slug: 2018-02-05-puppeteer-network-throttle
---

[Puppeteer](https://github.com/GoogleChrome/puppeteer) is an awesome way to run Chrome (or Chromium) in _headless_ mode, i.e. load and interact with web pages without ever visually seeing them. 

> Why would this be useful?  
> A headless browser is a great way to automate testing, even on remote server machines! 

Puppeteer provides a nice interface through [Node](https://nodejs.org/en/) to script any interactions you can have with a page, like entering input in a textbox, clicking a button and so on. It also allows you grab screenshots or convert web pages into PDFs.

Now, Puppeteer does not come with support for Network throttling out of the box, but it does have a way to communicate directly with the [Chrome DevTools](https://developer.chrome.com/devtools), which have support for throttling. The DevTools property in question is `Network.emulateNetworkConditions`, and it expects a configuration with the following attributes:

{{< highlight JavaScript >}}
let config = {
  // Whether chrome should simulate
  // the absence of connectivity
  'offline': false,
  // Simulated download speed (bytes/s)
  'downloadThroughput': 500,
  // Simulated upload speed (bytes/s)
  'uploadThroughput': 500,
  // Simulated latency (ms)
  'latency': 20
}
{{< / highlight >}}

As an example, lets say you wanted to load this site with a _200 Kb/s_ connection and _20 ms_ latency, and take a screenshot before shutting down, you could use the following script:

{{< highlight JavaScript "linenos=table" >}}
const puppeteer = require('puppeteer')

puppeteer.launch().then(async browser => {
  // Create a new tab
  const page = await browser.newPage()

  // Connect to Chrome DevTools
  const client = await page.target().createCDPSession()

  // Set throttling property
  await client.send('Network.emulateNetworkConditions', {
    'offline': false,
    'downloadThroughput': 200 * 1024 / 8,
    'uploadThroughput': 200 * 1024 / 8,
    'latency': 20
  })

  // Navigate and take a screenshot
  await page.goto('https://fdalvi.github.io')
  await page.screenshot({path: 'screenshot.png'})
  await browser.close()
})
{{< / highlight >}}

Chrome DevTools also have some nice presets like 3G (and used to have a lot more!). Although the original presets are gone, a [source file in the Chromium repo](https://cs.chromium.org/chromium/src/tools/android/loading/emulation.py) still has the presets, listed below for convenience. Just replace the second option in line 11 with `NETWORK_PRESETS['...']` to use the preset of your choice. These values are probably average values for each type of connection, and all of the `/ 8` operations are because network speed is commonly measured in _bits/s_, while DevTools expects the throughputs in _bytes/s_!

{{< highlight JavaScript >}}
let NETWORK_PRESETS = {
  'GPRS': {
    'offline': false,
    'downloadThroughput': 50 * 1024 / 8,
    'uploadThroughput': 20 * 1024 / 8,
    'latency': 500
  },
  'Regular2G': {
    'offline': false,
    'downloadThroughput': 250 * 1024 / 8,
    'uploadThroughput': 50 * 1024 / 8,
    'latency': 300
  },
  'Good2G': {
    'offline': false,
    'downloadThroughput': 450 * 1024 / 8,
    'uploadThroughput': 150 * 1024 / 8,
    'latency': 150
  },
  'Regular3G': {
    'offline': false,
    'downloadThroughput': 750 * 1024 / 8,
    'uploadThroughput': 250 * 1024 / 8,
    'latency': 100
  },
  'Good3G': {
    'offline': false,
    'downloadThroughput': 1.5 * 1024 * 1024 / 8,
    'uploadThroughput': 750 * 1024 / 8,
    'latency': 40
  },
  'Regular4G': {
    'offline': false,
    'downloadThroughput': 4 * 1024 * 1024 / 8,
    'uploadThroughput': 3 * 1024 * 1024 / 8,
    'latency': 20
  },
  'DSL': {
    'offline': false,
    'downloadThroughput': 2 * 1024 * 1024 / 8,
    'uploadThroughput': 1 * 1024 * 1024 / 8,
    'latency': 5
  },
  'WiFi': {
    'offline': false,
    'downloadThroughput': 30 * 1024 * 1024 / 8,
    'uploadThroughput': 15 * 1024 * 1024 / 8,
    'latency': 2
  }
}
{{< / highlight >}}