---
layout: post
title: "Scraping and Making in Go"
date: 2014-06-07 09:21:35 -0700
comments: true
categories:
    - Go
    - FamilySearch
---
Over the past week I've been experimenting with the [FamilySearch](https://familysearch.org/)
[API](https://familysearch.org/developers/docs/api/resources). I'm interested in building some applications 
against this API, and since I've been learning Go lately I figured this would be a good project to work on. I looked at 
the community projects that are already around, and there weren't any in Go (which really was expected). The API and 
DataTypes are well documented, so I started building out [go-family-search](https://github.com/boushley/go-family-search).

## The Hard Way
The first day I knew I needed to start getting the DataTypes written out, so I wrote out the Address data object
[by hand](https://github.com/boushley/go-family-search/commit/58cbc1b0839abe9dfbe49fcb053ab94a177868db) and was
happy to have gotten started on the project. However the more I thought about what I was attempting to do the
more I realized manually writing each of those DataTypes didn't sound like fun. More than that the API docs are so well
organized and accessible that I could see my writing would be fairly mechanical.

## Scraping to the Rescue!
So I started work on writing a scraper that would scrape the DataTypes listing page to find which types I wanted and 
then for each type scrape the definition from the corresponding page. It took me a couple of nights to work through the 
process and build out the tool, but then in under a minute all of the DataTypes were created for me. Now the foundation 
that is needed to build out the API calls is ready to Go (get it?).

### What Went Well
Because I was working in Go I didn't have to spend much time worrying about the formatting of the code. The built in 
parsers and printers meant that as long as the code was correct, all the spacing and formatting would be taken care of. 
It also improved my feedback loop since I didn't have to wait for the tool to finish and then attempt a `go build`, the 
parser would give me errors before the go file was even written to disk.

### Not So Good
There were some pieces of this that were more difficult than they needed to be though. Working in newer languages like 
Go always brings some extra challenges because the tools you want may not be available. In trying to parse information 
out of an HTML document I ended up using a [nice tool](https://godoc.org/code.google.com/p/go.net/html) that does HTML
tokenizing and parsing, but this is still a lower level tool than most people would want for performing web scraping. 
The upside to that being that I now know of at least one more project that it would be nice to write in Go, something 
that gives you a css selector type query system, similar to jQuery or document.querySelectorAll.

## Conclusion
In the end I still really enjoy writing Go. The language is new and so there are not nearly as many established tools 
and projects, but the language really feels like it gives you a solid set of foundational tools.
