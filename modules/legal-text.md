---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.2
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Text Preprocessing

This lab will provide an introduction to manipulating strings and chunking sentences.

## Topics Covered
- How to tokenize text
- How to stem text
- How to chunk text

### Table of Contents

[The Data](#section-data)<br>

1 - [Tokenization](#1)<br>

2 - [Stemming](#section-2)<br>

3 - [Chunking](#section-3)<br>

```{code-cell} ipython3
!pip install nltk svgling
```

---

## The Data <a id='data'></a>


In this notebook, you'll be working with the text of each country’s statement from the General Debate in annual sessions of the United Nations General Assembly. This dataset is separated by country, session and year and tagged for each, and has over forty years of data from different countries.


+++

### Visualizing data

+++

Run the below cells and take a look at a sample of the data that we'll be working with.

```{code-cell} ipython3
import pandas as pd
data = pd.read_csv("https://cal-icor.github.io/textbook.data/ucb/legal-123/un-general-debates.zip", compression='zip')
```

```{code-cell} ipython3
data.head()
```

## Tokenization  <a id='1'></a>

+++

Tokenization is defined as <b>the process of segmenting running text into words and sentences</b>.

+++

### Why do we need to tokenize text

+++

Electronic text is a linear sequence of symbols. Before any processing is to be done, text needs to be segmented into linguistic units, and this process is called tokenization.

+++

We usually look at grammar and meaning at the level of words, related to each other within sentences, within each document. So if we're starting with raw text, we first need to split the text into sentences, and those sentences into words -- which we call "tokens".

+++

### How to tokenize

+++

You might imagine that the easiest way to identify sentences is to split the document at every period '.', and to split the sentences using white space to get the words.

```{code-cell} ipython3
# using the split function to create tokens
paragraph = data['text'][0]
sentences = paragraph.split(".")
for s in sentences[:5]:
    print(s + '\n')
```

Then to split sentences further into words. Complete the code below to split sentence according to spaces.

```{code-cell} ipython3
sentence = "What kind of patterns do you see in this graph?"
tokens = ...
tokens
```

We'll stop here as NLTK provides handy tools for us to use.

+++

### NLTK

+++

NLTK (Natural Language Toolkit) is a platform for building Python programs to work with human language data

```{code-cell} ipython3
import nltk
# run the below commented command if error
# nltk.download('punkt_tab')
```

```{code-cell} ipython3
# create sentence tokens
speech = data['text'][4]
sents = nltk.sent_tokenize(speech)
sents[:3]
```

```{code-cell} ipython3
s4 = "At eight o'clock on Thursday morning Arthur didn't feel very good."
nltk.word_tokenize(s4)
```

nltk recognized that "o'clock" is one word and separated "didn't" into "did" and "n't"

+++

For more complicated metrics, it's easier to use NLTK's classes and methods.

```{code-cell} ipython3
# Find the 10 most common tokens
tokens = nltk.word_tokenize(speech)
fd = nltk.collocations.FreqDist(tokens)
fd.most_common()[:10]
```

Not so interesting as the most common words seem to be words that have no particular meanings.

+++

A common step in text analysis is to remove noise. *However*, what you deem "noise" is not only very important but also dependent on the project at hand. For the purposes of today, we will discuss two common categories of strings often considered "noise". 

- Punctuation: While important for sentence analysis, punctuation will get in the way of word frequency and n-gram analyses. They will also affect any clustering on topic modeling.

- Stopwords: Stopwords are the most frequent words in any given language. Words like "the", "a", "that", etc. are considered not semantically important, and would also skew any frequency or n-gram analysis.

+++

<b>Question</b> Write a function below that takes a string as an argument and returns a list of words without punctuation or stopwords.

`punctuation` is a list of punctuation strings, and we have created the list `stop_words` for you.

Hint: first you'll want to remove punctuation, then tokenize, then remove stop words. Make sure you account for upper and lower case!

```{code-cell} ipython3
def rem_punc_stop(text):
    
    from string import punctuation
    from nltk.corpus import stopwords
    
    stop_words = stopwords.words("english")
    
    ...
```

Now rerun your frequency analysis without the noise by completing the code below:

```{code-cell} ipython3
# nltk.download('stopwords')
tokens_reduced = ...
fd_reduced = nltk.collocations.FreqDist(tokens_reduced)
fd_reduced.most_common()[:10]
```

Now our analysis is much more informational and revealing.

+++

## Stemming <a id='2'></a>

+++

In NLP it is often the case that the specific form of a word is not as important as the idea to which it refers. For example, if you are trying to identify the topic of a document, counting 'running', 'runs', 'ran', and 'run' as four separate words is not useful. Reducing words to their stems is a process called stemming.

A popular stemming implementation is the Snowball Stemmer, which is based on the Porter Stemmer. Its algorithm looks at word forms and does things like drop final 's's, 'ed's, and 'ing's.

Just like the tokenizers, we first have to create a stemmer object with the language we are using. Refer to [this documentation](http://www.nltk.org/howto/stem.html) to create a snowball stemmer.

```{code-cell} ipython3
snowball = nltk.SnowballStemmer('english')
```

Now, we can try stemming some words

```{code-cell} ipython3
snowball.stem('running')
```

```{code-cell} ipython3
snowball.stem('eats')
```

```{code-cell} ipython3
snowball.stem('embarassed')
```

Snowball is a very fast algorithm, but it has a lot of edge cases. In some cases, words with the same stem are reduced to two different stems

```{code-cell} ipython3
snowball.stem('cylinder'), snowball.stem('cylindrical')
```

Sometimes two different words are reduced to the same stem.

```{code-cell} ipython3
snowball.stem('vacation'), snowball.stem('vacate')
```

<b>Question</b> How would the above two situations affect our text analysis?

+++

Your answer here

+++

## Chunking<a id='3'></a>

+++

<b>POS tagging</b> The process of classifying words into their parts of speech and labeling them accordingly is known as part-of-speech tagging.

Take a look at different [POS tags](http://www.ling.upenn.edu/courses/Fall_2003/ling001/penn_treebank_pos.html)

```{code-cell} ipython3
nltk.download('averaged_perceptron_tagger_eng')
tagged = nltk.pos_tag(tokens[2:8])
tagged
```

We may want to work with larger segments of text than single words (but still smaller than a sentence). For instance, in the sentence "The black cat climbed over the tall fence", we might want to treat "The black cat" as one thing (the subject), "climbed over" as a distinct act, and "the tall fence" as another thing (the object). The first and third sequences are noun phrases, and the second is a verb phrase.

We can separate these phrases by "chunking" the sentence, i.e. splitting it into larger chunks than individual tokens. This is also an important step toward identifying entities, which are often represented by more than one word. You can probably imagine certain patterns that would define a noun phrase, using part of speech tags. For instance, a determiner (e.g. an article like "the") could be concatenated onto the noun that follows it. If there's an adjective between them, we can include that too.

To define rules about how to structure words based on their part of speech tags, we use a grammar (in this case, a "chunk grammar"). NLTK provides a RegexpParser that takes as input a grammar composed of regular expressions (which define patterns in text, we'll learn it in later labs). The grammar is defined as a string, with one line for each rule we define. Each rule starts with the label we want to assign to the chunk (e.g. NP for "noun phrase"), followed by a colon, then an expression in regex-like notation that will be matched to tokens' POS (part-of-speech) tags.

We can define a single rule for a noun phrase like this. The rule allows 0 or 1 determiner, then 0 or more adjectives, and finally at least 1 noun. (By using 'NN.*' as the last POS tag, we can match 'NN', 'NNP' for a proper noun, or 'NNS' for a plural noun.) If a matching sequence of tokens is found, it will be labeled 'NP'.

```{code-cell} ipython3
grammar = "NP: {<DT>?<JJ>*<NN.*>+}"
```

We create a chunk parser object by supplying this grammar, then use it to parse a sentence into chunks. The sentence we want to parse must already be POS-tagged, since our grammar uses those POS tags to identify chunks. Let's try this on the second sentence of the speech we generated above.

```{code-cell} ipython3
# show then tokenize the second sentence
sent1 = sents[1]
print(sent1)
sent1_tokens = nltk.word_tokenize(sent1)


from nltk import RegexpParser

cp = RegexpParser(grammar)

sent1_tagged = nltk.pos_tag(sent1_tokens)
sent1_chunked = cp.parse(sent1_tagged)

print(sent1_chunked)
```

When we called print() on this chunked sentence, it printed out a nested list of nodes.

```{code-cell} ipython3
type(sent1_chunked)
```

The tree is pretty flat, because we defined a grammar that only grouped words into non-overlapping noun phrases, with no additional hierarchy above them. This is sometimes referred to as "shallow parsing". Run the next cell to see a representation of the tree.

```{code-cell} ipython3
import svgling
svgling.disable_nltk_png()
sent1_chunked
```

For more interesting trees, we need to add more phrase types, including those that contain other phrase types. 

```{code-cell} ipython3
grammar2 = r"""
    NP: {<DT>?<JJ>*<NN.*>+}      # Chunk sequences of DT, JJ, NN
    PP: {<IN><NP>}               # Chunk prepositions followed by NP
    VP: {<VB.*><NP|PP|CLAUSE>+} # Chunk verbs and their arguments
    CLAUSE: {<NP><VP>}           # Chunk NP, VP into a clause
    """
```

<b>Question:</b> write code that chunks the second sentence of the speech `sent2` using `grammar2`. Remember, you need to:
- create a RegexpParser using `grammar2`
- tokenize and POS tag `sent2`
- make a Tree by using your new parser to parse your tokenized, POS-tagged sentence

Save your Tree to the variable `sent2_chunked`

```{code-cell} ipython3
sent2 = sents[2]
print(sent2)

# your code here
...
...
...
sent2_chunked = ...

print(sent2_chunked)
```

```{code-cell} ipython3
import svgling
svgling.disable_nltk_png()
sent2_chunked
```

## Combining it all

+++

Write a function that takes in a table, tokenizes all speeches in it, removes noise, and returns a string of stems of all tokens.

Hint: any function from above that we can just grab and use?

+++

As a reminder, this is what our table looks like

```{code-cell} ipython3
data.head()
```

```{code-cell} ipython3
def does_it_all():
    ...
```

Let's apply our function on speeches from 2001.

First create a table that includes all 2001 speeches. Refer to [this doc](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.loc.html)

```{code-cell} ipython3
speech_2001 = ...
speech_2001.head()
```

Then create a new column in speech_2001 which contains the tokenized string.

```{code-cell} ipython3
speech_2001...
```

---
Notebook developed by: Tian Qin

Data Science Modules: http://data.berkeley.edu/education/modules
