# Generate Tweet

Train on tweet corpus and generate a response:

~~~
~$ python ./generate-tweet/main.py -t tweets.txt
~~~

Train with text corpus, use n-gram factor of 2, generate 20 responses
and return in a JSON array:

~~~
~$ python ./generate-tweet/main.py -f corpus.txt -n 2 -c 20 -j
~~~

# Corpus

To create new dataset files from the corpus:

~~~
~$ make corpus
~~~

These files are read and tokenized to train the model.

## Downloading Tweets

This method scrapes the Twitter webpage instead of using the provided
API because the amount of older tweets are limited.

Use the [Twitter Advanced Search](https://twitter.com/search-advanced)
and add a username to *From these accounts*. Set dates if you need to.

On the results page, click *Live* to put the statuses in chronological order.

Scroll down as needed to go back in through the archive. If this
becomes tedious, open of the JavaScript console and enter something
like this to let the browser do it:

~~~
> var interval = setInterval(function () { window.scrollBy(0, 500) }, 150)
~~~

And to stop the scrolling:

~~~
> clearInterval(interval)
~~~

Save the complete webpage using the browser's save function.

## Create a CSV Archive

To get the tweets out of the downloaded page, scrape the file and
format into a csv file:

~~~
~$ node ./get-tweets/scraper.js saved-tweets.html > tweets.csv
~~~

You can scrape multiple files and output the results together, even if
the dates overlap. The following scrapes the files, sorts the rows
oldest-first using the status id field, removes duplicate entries,
and compresses the file:

~~~
~$ node ./get-tweets/scraper.js *.html | sort -u -n -t',' | gzip > tweets.csv.gz
~~~

## Combine Archive Files

To update an archive file, just cat together and re-sort:

~~~
~$ gzcat tweets1.csv.gz tweets2.csv.gz | sort -u -n -t',' | gzip > tweets.csv.gz
~~~

Archive files are typically in the format:
`username-lastStatusId.csv.gz`, make sure to update the `Makefile`.

### CSV Format

Tweets are stored in a csv file with their *status id*, *timestamp*,
and *text*. The *status id* is an int64 (which JavaScript has trouble
with) so should be read as a string, the *timestamp* is in ISO 8601
format, and the *text* is a JSON escaped string. A typical entry row
looks like:

~~~
669750537252970496,2015-11-27T02:57:21.000Z,"escaped \"status\" text"
~~~