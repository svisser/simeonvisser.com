<!--
.. title: Why Wikidata is Great for Wikipedia
.. slug: why-wikidata-is-great-for-wikipedia
.. date: 2013/04/30 21:57:00
.. tags: wikipedia,wikidata,semantic-web
.. link:
.. description:
-->

A recent Hacker News post made the Wikidata project more widely known
in the tech community. It also created some confusion as the relevance of the
project was not widely understood. I think Wikidata will easily be among the
best projects for Wikipedia in 2013 and the future so it's good to explain
why that is.

### Wikidata solves a long-standing issue ###

First of all, it solves a long-standing issue that plagued many Wikipedia
articles. Wikipedia's goal is the make knowledge widely
available in many languages and many articles are indeed available in multiple
languages.

Next to each article is a list of links to articles about
the same subject in a different language. These links are manually added
by specifying the language code and the title of the article. For example, if
I want to include a sidebar link to the *Wikipedia* article
in the English language I would include:

<pre>
[[en:Wikipedia]]
</pre>

where `en` indicates the language code of the project. However, these
so-called interwiki links need to be added to every language. So every article
stores interwiki links to all other articles about that subject.

This is fine as long as 1) nothing needs to change and 2) each article
is unambiguously about the same subject. And this is where the problems begin:
often an article would be renamed and then all interwiki links on all other
Wikipedia projects would need to be updated.

This was not manually feasible so people developed bots to do this. Many bots
would regularly update articles across all languages purely to keep interwiki
links in sync.

With bots doing the hard work, all should be good, right? Not really because
human error often introduced links to articles about a very
closely but not exactly the same subject. For example, the article *New York*
might describe the city on some Wikipedia projects while it discusses the state
on others.

Bots don't understand this so they'll gladly review an article's interwiki links
and rampantly copy across any missing links to other articles. This ensures
interwiki links are the same in each language but it also means errors are
propagated. Even worse, it requires human intervention to fix any incorrect links. If a
Wikipedia contributor in some exotic language introduced a mistake in the interwiki
links it would often propagate to other projects until it was manually
fixed, *everywhere*.

Wikidata solves this by having one page per subject and by storing the
interwiki links there. So instead of letting articles link to all other
languages they now fetch the interwiki links from their
corresponding Wikidata page. This means interwiki links can be updated in
one place rather than in all places.

### Long-term benefits of Wikidata ###

Now that we can have a unique page per subject we can build
upon that foundation. The Wikidata page stores the interwiki links but it
can also store facts about the subject. This makes the Wikidata project even
more useful to Wikipedia.

For example, for cities it could store the number of inhabitants per census
and for famous individuals it could store the date of birth, date of death and
other relevant facts.

This makes it easy to:

- Generate the information box at the right side of many Wikipedia articles
- Produce tables with relevant information
- Make it possible to generate specific lists (e.g., all cities
  that had one million inhabitants in 1950).

That last point is worth elaborating upon: Wikipedia's articles are categorised
so it's currently easy to find people born in a particular year but it's not so
easy to find, e.g., all cities with a mayor born in 1950.

In a way the information is currently on Wikipedia but it's not so easy to
find as it requires the reader to manually connect these facts together.
The existence of Wikidata allows Wikipedia to turn into [Semantic Wikipedia](http://en.wikipedia.org/wiki/Wikipedia:Semantic_Wikipedia)
and that makes knowledge even more freely available than the project already does.