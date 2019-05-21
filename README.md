# TDMentions: A Dataset of Technical Debt Mentions in Online Posts (version 1.0)

TDMentions is a dataset that contains mentions of technical debt from Reddit, Hacker News, and Stack Exchange. It also contains a list of blog posts on Medium that were tagged as technical debt. The dataset currently contains approximately 32,000 items. 

The dataset is available at [http://dx.doi.org/10.5281/zenodo.2593142](http://dx.doi.org/10.5281/zenodo.2593142). 

## Data collection and processing

The dataset is mainly collected from existing datasets. We used data from:

- the archive of Reddit posts by Jason Baumgartner (available at [https://pushshift.io](https://pushshift.io), 
- the archive of Hacker News available at Google's BigQuery (available at [https://console.cloud.google.com/marketplace/details/y-combinator/hacker-news](https://console.cloud.google.com/marketplace/details/y-combinator/hacker-news)), and the Stack Exchange data dump (available at [https://archive.org/details/stackexchange](https://archive.org/details/stackexchange)). 
- the [GHTorrent](http://ghtorrent.org) project 
- the [GH Archive](https://www.gharchive.org)

The data set currently contains data from the start of each source/service until 2018-12-31. For GitHub, we currently only include data from 2015-01-01.

We use the regular expression `tech(nical)?[\s\-_]*?debt` to find mentions in all sources except for Medium. We decided to limit our matches to variations of technical debt and tech debt. Other shorter forms, such as TD, can result in too many false positives. For Medium, we used the tag `technical-debt`. 

## Data Format

The dataset is stored as a compressed (bzip2) JSON file with one JSON object per line. Each mention is represented as a JSON object with the following keys.

- `id`: the id used in the original source. We use the URL path to identify Medium posts.
- `body`: the text that contains the mention. This is either the comment or the title of the post. For Medium posts this is the title and subtitle (which might not mention technical debt, since posts are identified by the tag).
- `created_utc`: the time the item was posted in seconds since epoch in UTC. 
- `author`: the author of the item. We use the username or userid from the source.
- `source`: where the item was posted. Valid sources are:
    - HackerNews Comment
    - HackerNews Job
    - HackerNews Submission
    - Reddit Comment
    - Reddit Submission
    - StackExchange Answer
    - StackExchange Comment
    - StackExchange Question
    - Medium Post
- `meta`: Additional information about the item specific to the source. This includes, e.g., the subreddit a Reddit submission or comment was posted to, the score, etc. We try to use the same names, e.g., `score` and `num_comments` for keys that have the same meaning/information across multiple sources.

This is a sample item from Reddit:

```JSON
{
  "id": "ab8auf",
  "body": "Technical Debt Explained (x-post r/Eve)",
  "created_utc": 1546271789,
  "author": "totally_100_human",
  "source": "Reddit Submission",
  "meta": {
    "title": "Technical Debt Explained (x-post r/Eve)",
    "score": 1,
    "num_comments": 0,
    "url": "http://jestertrek.com/eve/technical-debt-2.png",
    "subreddit": "RCBRedditBot"
  }
}
```

## Sample Analyses

We decided to use JSON to store the data, since it is easy to work with from multiple programming languages. In the following examples, we use [`jq`](https://stedolan.github.io/jq/) to process the JSON.

### How many items are there for each source?

```
lbzip2 -cd postscomments.json.bz2 | jq '.source' | sort | uniq -c
```

### How many submissions that mentioned technical debt were posted each month?

```
lbzip2 -cd postscomments.json.bz2 | jq 'select(.source == "Reddit Submission") | .created_utc | strftime("%Y-%m")' | sort | uniq -c
```

### What are the titles of items that link (`meta.url`) to PDF documents?

```
lbzip2 -cd postscomments.json.bz2 | jq '. as $r | select(.meta.url?) | .meta.url | select(endswith(".pdf")) | $r.body'
```

### Please, I want CSV!

```
lbzip2 -cd postscomments.json.bz2 | jq -r '[.id, .body, .author] | @csv'
```

Note that you need to specify the keys you want to include for the CSV, so it is easier to either ignore the meta information or process each source.

# Limitations and Future updates

The current version of the dataset lacks GitHub data and Medium comments. GitHub data will be added in the next update. Medium comments (responses) will be added in a future update if we find a good way to represent these.