# Temporal

> Hosts: Marco & Jannes

We talked about the new Temporal API and why the time has come finally ditch the old `Date` API. Marco has a talk about this, which we used in this session. 

## `Date` was a mistake
* the `Date` API is a mess, which is the resulting of copying over `java.util.Date` in the 90s
* Java has long moved past this API, but for backwards compatibility reasons, JavaScript has not
* In the session we went through the quiz at [jsdate.wtf](https://jsdate.wtf/) together.
  * We learned that many of the quirks are in the constructor
* `Date` objects are mutable: can cause subtle errors due to passed references
* The shortcomings of `Date` has lead to a zoo of date libraries, such as `moment`, `date-fns`, `luxon` and `dayJS`. They all have their own API and are not interoperable with each other. And of course they add to your bundle size.

The Temporal API is designed to replace `Date` and is in the works since 2017.

## 2026 is the year of Temporal

|            | Status | Comment                             |
|------------|--------|-------------------------------------|
| Chromium   | ✅      | v144, January 2026                  |
| Firefox    | ✅      | v139, May 2025                      |
| Safari     | ❌      | in Technology preview + feature flag |
| Node       | ✅      | v26, May 2026                       |
| Deno       | ✅      | v2.7, February 2026                 |
| Bun        | ❌      | depends on [JSC](https://github.com/apple-opensource/JavaScriptCore) from webkit  |
| Typescript | ✅      | Typescript 6 includes Temporal types |

## Conceptual differences
* Instead of a single `Date` class there is a class for every kind of date/time representation, such as `PlainDate`, `ZonedDateTime` etc.
* All classes are immutable
* Temporal is “fail-fast” and not lenient as `Date`
* Proper timezone support

## A not too deep dive into the API

We played around with the API in the browser console.

TODO:

## Performance comparison

TODO: add table here

Bottom line: Migrating to Temporal will probably reduce your bundle size and improve performance (although that will only be relevant for some project). However, the real gain is the improved API and the reduced likelihood of bugs.


## Migration to Temporal
* Marco has done that for an application that makes heavy use of dates and times, and it was a smooth process that was done within a week (without working 100% of the time on it)
* AI has a good understanding of the API, because the proposal exists for a long enough time.
* The decision of whether to use `PlainDate`, `ZonedDateTime` etc. for a given `Date` in the old code should not be left to an AI entirely.
* The needed polyfill for Safari users is not a problem.