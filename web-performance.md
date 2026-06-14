# Web performance and how to measure it
_Jannes Bantje, TNG_

This was an ad-hoc shortened version of a TNG-internal workshop I held once.


## 1. What is performance actually?
* You should care about the performance of your web application because users quickly get annoyed if the app feels broken. In other words: it’s just good UX
* Performance on the web also has a perception psychology aspect: oftentimes it’s more about making waiting times more acceptable to your users than about actually making things faster. For example, you can show a skeleton screen while the content is loading, which makes the waiting time feel shorter.
* On a technical level web applications are special in a few respects:
  * All the resources need to be sent via the network everytime a users starts the app (in contrast to desktop of mobile applications which are persisted on the user’s device)
  * We need to target three runtimes, aka browser engines
  * users might have restricted resources in terms of local compute and network bandwidth (especially on mobile).
* A reasonable  but too harsh goal for snappy application is to be able to react to user input within the usual 60fps, which means that only 16ms are available to make the UI react to some user interaction.
* The 16ms are important for continuous changes on the page like animations. More binary things like going to a new route may take up to 200ms in order to still be perceived as fast.

## 2. The browser render pipeline

The rough model of how a browser renders a web page is as follows:

```text
+--------------+      +---------+      +----------+      +---------+      +-------------+
|  JavaScript  |----->|  Style  |----->|  Layout  |----->|  Paint  |----->|  Composite  |
+--------------+      +---------+      +----------+      +---------+      +-------------+
```

1. **JavaScript:** handle some work resulting in DOM/CSS change. This work could be many things: and event handler, a timer, the initial document being loaded etc.
1. **Style:** compute styles for every DOM element (in devtools: “Computed” tab)
1. **Layout:** calculate geometry and position of each node. This is very complicated because the elements are interdependent: the size of one element may depend on the size of another element, etc.
1. **Paint:** draw pixels for text, colours, images, borders etc. onto separate layers
1. **Composite:** combine layers (on GPU) according to the layout. This is very fast.

Depending on what you change on the page, some of these steps can be omitted: for changing the color of something does not require a recalculation of the layout (which is called a “full reflow”). That is why animation usually use `transform: ...` because that one can run on the GPU and does not require a full reflow. In contrast, changing the `width` of an element would require a full reflow.

## 3. Core Web vitals

* Project by Google: define a small set of metrics that measure the performance of a web application in a way that is relevant for users. These metrics are also used by Google to rank search results.

The current set are the following three:

1. **Largest Contentful Paint (LCP):** measures the time it takes for the largest content element to be rendered on the page (often banner images). This is a good proxy for how long it takes for the main content of the page to be visible to the user.
2. **Interaction to Next Paint (INP):** measures the time it takes for the page to respond to user interactions with a paint step. This is a good proxy for the perceived responseiveness of the page.
3. **Cumulative Layout Shift (CLS):** measures the amount of unexpected layout shifts that occur during the loading of the page. This measures visual stability.

These three metrics are captured by Chromium devtools directly, just open the “Performance” tab” and take a look. Chrome even captures this anonymously for all users and reports it to Google. You can inspect this at [CrUX Vis](https://cruxvis.withgoogle.com/#/). The data there is better because it is not coming from your beefy dev machine behind a fast network connection, but from real users with all kinds of devices and network conditions.

## 4. A tour through the Chrome dev tools’ performance tab

Here the session became a bit more hands-on. We went through the following steps:
* The three CWV metrics are not just captured, the devtools also tell your what the LCP element is (as pages are rendered progressively this often changes until the page settles), what the interactions for INP were and what elements shifted for CLS.
* The full complexity of the performance tab however only becomes available once you make a recording: you can start a new recording + page reload or record just a single interaction. Afterwards you will see something like this: ![Recording in performance tab](/assets/screenshot-perf-tab.png)
* The top row is a very important navigation timeline, showing your when activity spikes occur. 
* There is also a network section, such that you do not have to switch tabs in order to figure out how long network requests took and when they were initiated.
* The main section is the flame chart, which shows you what the browser was doing at any point in time. In order to understand this, a bit more theory is needed, which is covered in the next section.

## 5. The Javascript event loop
We have to start with some fundamental concepts:
* Javascript is single threaded, but the runtime (aka browser engine) is doing a lot of stuff in the background and in parallel and controls when JS is executed: for example the runtime realizes that a button has been clicked and then performs the javascript code for the event handler. 

### The macrotask queue
* The runtime places these “javascript callbacks” in the macrotask queue.
* The runtime can priorize, i.e. decide that a click event handler is executed earlier that a task created with `requestIdleCallback()`.
* However, every macrotask is executed until completion (unlike for example how operating system scheduling works)
* Then thread working through the macrotask queue is the main thread and the gray bars in the chart are the macro tasks.
* Inside of them we also see the browser render pipeline:
  * The yellow parts are the javascript being executed
  * this is followed by some layouting, painting, composing etc. if this has caused DOM changes. 

### The microtask queue

* This is a high-priority queue _inside a a macro task_ that works through computation triggered by for example `Promises (.then(), .catch(), .finally())`, `queueMicrotask()`, `MutationObserver`
* It is getting processed after the main task code is done


<details>

<summary>
Small exercise for the even loop
</summary>

What is the output of the following:

```javascript
async function async1() {
  console.log('A');
  await async2();
  console.log('B');
}

async function async2() {
  console.log('C');
}

console.log('D');

setTimeout(() => console.log('E'), 0);

async1();

new Promise((resolve) => {
  console.log('F');
  resolve();
}).then(() => console.log('G'));

console.log('H');
```

</details>


## An example that ties it all together

We took a look at a toy example of so-called “layout-thrashing”, a performance problem cause by too many reflows in a short amount of time

<details>

<summary>
Layout Thrashing Demo
</summary>


```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Layout Thrashing Demo</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            max-width: 800px;
            margin: 0 auto;
        }
        .controls {
            margin-bottom: 20px;
            display: flex;
            gap: 10px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        #bad {
            background: #ff6b6b;
            border: none;
            color: white;
        }
        #good {
            background: #51cf66;
            border: none;
            color: white;
        }
        #reset {
            background: #868e96;
            border: none;
            color: white;
        }
        #result {
            padding: 15px;
            background: #f1f3f5;
            border-radius: 5px;
            margin-bottom: 20px;
            min-height: 20px;
        }
        .container {
            display: flex;
            flex-wrap: wrap;
            gap: 5px;
        }
        .box {
            width: 50px;
            height: 50px;
            background: #339af0;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 12px;
        }
    </style>
</head>
<body>
<h1>Layout Thrashing Demo</h1>
<p>Open DevTools (F12) → Performance tab to see the difference!</p>

<div class="controls">
    <button id="bad">❌ With Thrashing</button>
    <button id="good">✅ Optimized</button>
    <button id="reset">Reset</button>
</div>

<div id="result"></div>

<div class="container" id="container"></div>

<script>
    const container = document.getElementById("container");
    const result = document.getElementById("result");

    // Create 200 boxes
    function createBoxes() {
        container.innerHTML = "";
        for (let i = 0; i < 2000; i++) {
            const box = document.createElement("div");
            box.className = "box";
            box.textContent = i;
            container.appendChild(box);
        }
    }

    // ❌ BAD: Layout Thrashing - reads and writes interleaved
    document.getElementById("bad").addEventListener("click", () => {
        const boxes = document.querySelectorAll(".box");
        const start = performance.now();

        boxes.forEach((box) => {
            // READ: Forces layout calculation
            const width = box.offsetWidth;
            // WRITE: Invalidates layout
            box.style.width = width + 1 + "px";
            // This pattern repeats 200 times = 200 forced layouts!
        });

        const time = (performance.now() - start).toFixed(2);
        result.innerHTML = `❌ <strong>Thrashing:</strong> ${time}ms`;
    });

    // ✅ GOOD: Batched - all reads first, then all writes
    document.getElementById("good").addEventListener("click", () => {
        const boxes = document.querySelectorAll(".box");
        const start = performance.now();

        // BATCH READ: Single layout calculation
        const widths = Array.from(boxes).map((box) => box.offsetWidth);

        // BATCH WRITE: Layout invalidated once at the end
        boxes.forEach((box, i) => {
            box.style.width = widths[i] + 1 + "px";
        });

        const time = (performance.now() - start).toFixed(2);
        result.innerHTML = `✅ <strong>Optimized:</strong> ${time}ms`;
    });

    // Reset boxes
    document.getElementById("reset").addEventListener("click", () => {
        createBoxes();
        result.innerHTML = "Reset complete";
    });
</script>
</body>
</html>

```

</details>

The two buttons increase the width of each of the 2000 boxes by one pixel. Via the devtools one can find out the following:
* The bad way of doing it causes a very bad INP
* This is caused by the loop interweaving read and write operations of the boxes’ widths. If one such step is done and the browser is tasked to read the width of the next box it has to layout again. The cause is that the `width` of some element has changed and thus an accurate value of that read operation cannot not be guaranteed without an updated layout.
