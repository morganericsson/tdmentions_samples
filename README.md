# TD Mentions

The data from Hacker News, Medium, Reddit, and Stack Exchange is structured as follows. The content is divided into *submissions* and *comments*, where a submission is an initial question, comment, or reference to a website. Each submission can have comments. 

*Submissions*

The submissions data contains the following attributes:

- _id_, internal row id.
- _title_, the title of the submission.
- _author_, an internal representation of the user. Uniquely identifies a user within this dataset.
- _text_, the text of the submission (if available).
- _url_, the url of the submission (if available).
- _subforum_, the part of the site the submission was submitted to (if applicable), e.g., subreddit or Stack Exchange site.
- _score_, the score of the submission as a numerical value, e.g., sum of up and downvotes, claps, etc.
- _created_, timestamp of when the submission was submitted (seconds UTC).
- _source_, source of the submission, e.g., Reddit, Medium, ... .

*Comments*

The comments data contains the following attributes:

- _id_, internal row id.
- _author_, an internal representation of the user. Uniquely identifies a user within this dataset.
- _text_, the text of the comment.
- _subforum_, the part of the site the comment was submitted to (if applicable), e.g., subreddit or Stack Exchange site.
- _parent_, the parent comment of this comment. Empty/null if the comment is directly on the submission.
- _submission_, the submission this comment belongs to. 
- _score_, the score of the submission as a numerical value, e.g., sum of up and downvotes, claps, etc.
- _created_, timestamp of when the comment was submitted (seconds UTC).

*TD Mentions*

The submissions and comments data is the closure of all mentions of technical debt. For example, if a comment mentions technical debt, the submission and all other comments are included, even if they do not mention technical debt, to provide context. The *TD Mentions* data is a collection of internal ids of submissions and comments that explicitly mentions technical debt. It contains the following attributes:

- _id_, the id of the entity that mentions technical debt
- _class_, the class of the entity, submission or comment

*Mapping to source*

To allow for validation of the dataset or studies that require additional data, a mapping between the internal id of submissions and comments, and the source, e.g., submission on Reddit is available on request. To ensure that the data is available, this mapping uses the internal id of the dataset that was used, e.g., the dumps of Reddit.