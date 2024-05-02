---
title: "Tracking changes in Lighthouse performance score"
slug: "vtex-io-documentation-tracking-changes-in-lighthouse-performance-score"
hidden: false
createdAt: "2021-04-07T18:18:24.559Z"
updatedAt: "2022-12-13T20:17:44.828Z"
---

> ℹ️ In this tutorial, we'll use [Lighthouse Scoring Calculator](https://googlechrome.github.io/lighthouse/scorecalc/#version=6), a tool created by Google that allows you to play with Lighthouse performance metrics and define which thresholds you should aim for to achieve the desired Performance Score.

It's possible that a change you expected to cause a considerable increase in the *Performance Score* doesn't affect it at all. Conversely, a seemingly small change may affect the score significantly. The same applies in reverse for possible regressions in performance.

In the following sections, we'll explore some of the factors that might be the reason for this. However, before proceeding, it's important to remember that:

1. The *Performance Score* is not linear. Actually, **the *Performance Score* is a weighted average** of the following six in the lab metrics:

![pfscore](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-0.png)

2. **The *Metrics' Scores* are not linear.** Lighthouse metrics follow their own rules. To better illustrate that, take the following example: imagine you have a *Largest Contentful Paint (LCP)* of 8s. You work on some improvements, and you reduce *LCP* by 2 seconds. When you look at the *Metric Score* you notice it went from 3 to 13, increasing the overall *Performance Score* by 2 points (from 76 to 78).

![8to6](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-1.gif)

You're not satisfied yet, so you work on more changes and you manage to take *LCP* from 6s to 4s, which also corresponds to a reduction of 2s. However, this time, when you look at the *Metric Score*, you notice it went from 13 to 50, improving the overall *Performance Score* by 10 points (from 78 to 88).

![6to4](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-2.gif)

Notice that in both cases, there was a reduction of 2s in the *LCP* time. However, in the first case, this improvement led to a gain of 10 points (from 3 to 13) in the *Metric Score*. Meanwhile, in the second case, the same reduction of 2s led to a gain of 37 points (from 13 to 50).

With that in mind, let's dive deeper and explore some other reasons for "unexpected" behaviors from Lighthouse.

## Going from nothing to nothing

When you have a metric that scores too low, a seemingly significant change may not affect the *Performance Score* as expected.

To better illustrate that, let's investigate the *Largest Contentful Paint (LCP)* metric.

*LCP* has a weight of 25% on the *Performance Score*. Therefore, to affect the overall *Performance Score*, the *LCP* score must be at least 2, which would correspond to 1 point (rounding up 2\*0.25=0,5) in the *Performance Score*.

> ℹ️ When calculating the *Performance Score*, decimal values greater than 0.5 are rounded up to the next largest whole number.

With that in mind, suppose *LCP* went from 12s to 8s.

![12to8](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-3.gif)

Notice that, even if this could be considered a significant improvement, any LCP time greater than approximately 8s yields a *Performance Score* of 0. So, in this example, the *LCP* score went from 0 to 3, leading to a gain equivalent to 1 in the *Performance Score* (rounding up 3\*0.25=0.75).

In this sense, to have an idea of when the *Metric Score* is too slow, we recommend that you take a look at the maximum values for starting punctuating with each metric.

![maxvalue](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-4.png)

> ℹ️ Notice that the metrics scores will be 0 for any value above the given limits.

We also recommend being attentive to the following values:

|                                   | Good       | Needs improvement | Poor        |
| --------------------------------- | :--------- | :---------------- | :---------- |
| **First Contentful Paint (FCP**)  | 0 - 1000ms | 1000ms - 3000ms   | Over 3000ms |
| **Speed Index (SI)**              | 0 - 4300ms | 4400ms - 5800ms   | Over 5800ms |
| **Largest Contentful Paint(LCP)** | 0 - 2500ms | 2500ms - 4000ms   | Over 4000ms |
| **Time to Interactive (TTI)**     | 0 - 3800ms | 3900ms - 7300ms   | Over 7300ms |
| **Total Blocking Time (TBT)**     | 0 - 300ms  | 300ms - 600ms     | Over 600ms  |
| **Cumulative Layout Shift(CLS)**  | 0 - 0.1    | 0.1 - 0.25        | Over 0.25   |

## Cascading effect

If you're struggling with the Lighthouse Performance audit, we recommend that you pay attention to *First Contentful Paint (FCP).*

The *FCP* metric measures how long it takes to display the very first piece of content of the page.

If *FCP* time is too long, it will affect almost all of the other metrics, dragging the *Performance Score* way down.

A slow *FCP* time might mean that, for some reason, the server is taking too long to respond. It might also mean that **Server-Side Rendering (SSR) is not working.**

If SSR is not working correctly, the page needs to download, parse, and execute all of its JS files so it can finally display any content at all.

![page](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-5.gif)

Notice that many metrics, such as *SI*, *LCP*, *TTI*, depend on the content being displayed as soon as possible. Consequently, they are directly affected by *FCP*, making this metric extremely important for the overall *Performance Score*.

Take the following example:

![FCP-Lab-Data](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-6.png)

## Variabilities from run to run

If the *Performance Score* of your page keeps changing, keep in mind that different network conditions might be affecting the score and triggering cascading effects.

For example, if you run Lighthouse when the page is taking a bit too long to respond, most of the metrics might be affected. That will generate a much worse score.

Ideally, the root cause must be fixed. But, in general, we recommend that you [**run the test around five times and pick the median value.**](https://developers.google.com/web/tools/lighthouse/variability#run_lighthouse_multiple_times)

[An in-depth explanation of score variability can be found here.](https://developers.google.com/web/tools/lighthouse/variability)

## Perceived performance matters

Web performance is a relative term. From the user's point of view, it's a perception of how he experiences page loading and how responsive a page feels.

Some metrics, especially *Largest Contentful Paint (LCP)*, *Speed Index (SI)*, and *Cumulative Layout Shift (CLS)*, are based on perception rather than pure speed. In broad terms, they track how long it takes for the content above-the-fold to be visibly ready.

These metrics are affected by conditions that don't necessarily make the page slower. That's why sometimes you may have the feeling that the *Performance Score* changed without any apparent reason. They can be affected, for example, by elements that shift around, images without pre-set dimensions, large images, modal windows, components added via JS, etc.

Notice that these three metrics have a huge impact on the overall *Performance Score* and account for 45 points of the total.

![atfp](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/vtex-io-documentation-tracking-changes-in-lighthouse-performance-score-7.png)

## Everything is connected

Sometimes metrics can be conflicting between them and something that should improve one metric impacts another, lowering the overall *Performance Score*.

For example, lazy-loading images might improve the *Time to Interactive (TTI)* metric because JS is loaded earlier. However, by delaying the time taken to render the largest element of the page, the *Largest Contentful Paint (LCP)* is negatively affected.

The advice here, which applies to other cases as well, is to keep in mind which metrics you are expecting to improve and to mind potential drawbacks in the others.

Besides that, to paint a clearer picture of what is happening, track individual measurements instead of only the total *Performance Score*.
