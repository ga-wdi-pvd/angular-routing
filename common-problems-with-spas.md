# Common Problems with Single Page Applications

## Bookmarking

Every web page has a URL. We link to pages via these URLs and bookmark them. Without relying on URLs, how do we represent state in an application?

Trello is an excellent example of a SPA. Try clicking around on a few cards and watch how the URL changes.

## Search Engine Optimization

How can Google index our content if the content is hidden behind event listeners and ajax requests?

Serve a raw html version as well? https://cdnjs.com/libraries/backbone.js/tutorials/seo-for-single-page-apps
Start with a fully funcional html app, and "progressively enhance"
Analytics

Similar to the above, URL's are often the number one indicator for collecting analytics (counting page views). How do measure who is doing what when there's only one page load per person?

https://developers.google.com/analytics/devguides/collection/analyticsjs/single-page-applications

## Progressive Enhancement vs Graceful degradation

Some users have JS disabled, or at least more recent features are unavailable. Should we build an app that progresses as more features become available, or an app that degrades gracefully? (Like Gmail's this is taking a long time to load...)

https://jakearchibald.com/2013/progressive-enhancement-still-important/
How do we reflect this new state?

## Deep Linking

Let's say we wanted to share these results with others. How could we do that?

Ask them to follow our steps exactly?

Wouldn't it be nice if we could reflect the state of our application in the URL?

In the context of the World Wide Web, deep linking is the use of a hyperlink that links to a specific, generally searchable or indexed, piece of web content on a website (e.g. "http://example.com/path/page"), rather than the website's home page (e.g. "http://example.com/").
A practical example of this is Trello's card detail view.

A common solution before HTML5 was to add a hash to the URL with window.location.hash

## HTML5 History API

Shameless plug, Jesse actually wrote an article for CSS tricks on this! http://css-tricks.com/rethinking-dynamic-page-replacing-content

He was reading [this article](http://diveintohtml5.info/history.html) late one night and had the idea!

But seriously, the HTML5 history API allows us to manipulate the URL without a hash and without refreshing the page.
