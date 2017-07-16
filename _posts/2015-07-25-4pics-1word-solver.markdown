---
layout: post
title:  4 Pics 1 Word Solver with Python and Google App Engine
description: Four Pics One Word Solver implemented in Python and Google App Engine
---

## TL;DR: [http://four-pics-one-word-solver.appspot.com/][web-service]

One of the word puzzle game that I've been playing this past month is the popular
[4 Pics 1 Word][4pics1word] Game, from the Google Play Store page it says:
**4 pictures that have 1 word in common**.

For a non-native English speaker like me, with just pictures and random letters,
some words are hard to find so I thought of a way to find the correct words.
Looking at the game it provides the exact number of letters of the correct answer
and the most important of all the letter choices. So armed with these facts we can
easily tell if a candidate word is a subset of the letter choices.

But you may ask where to get those candidate words to compare with the letter choices?
Luckily, someone on the internet has compiled a dataset - a list of unique common words
that he extracted from Google Books. Click [here][mayzner] to learn more.

Now that we have our dataset - a corpus of 97,566 unique common words that can be found on
Google Books, we can then proceed to write a python script to test if our solution is correct.

{% highlight python %}
#!/usr/bin/env python
__doc__ = 'Solve 4 Pic 1 Word programmatically'

import sys

GOOGLE_CORPUS_DATA = 'google-books-common-words.txt'

def file_to_list(file):
    """ Convert a file to list of words
    """
    lines = []
    with open(file, 'r') as f:
        lines = f.readlines()
    new_lines = [line.strip().split()[0] for line in lines]
    return new_lines

def check_length(word, size):
    """ Check if the `word` has lenght of `size`
    """
    return len(word) % size == 0

def in_subset(word, choices):
    """ Check if the `word` is a subset of `choices`
    """
    choices = list(choices)
    for c in word:
        if c not in choices:
            return False
        choices.remove(c)
    return True

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print 'Usage: python main.py <size> <characters>'
        sys.exit()
    choices = sys.argv[2].upper()
    size = int(sys.argv[1])
    words = file_to_list(GOOGLE_CORPUS_DATA)
    candidates = []
    for word in words:
        if check_length(word, size):
            if in_subset(word, choices):
                candidates.append(word)
    print '\t'.join(candidates)
    print 'Got %d word candidates' % len(candidates)
{% endhighlight %}

**Example Question:**

- Word length: 5
- Letter Choices: WATEJRNLVRLN
- Answer: WATER

The script got 53 word candidates. Cool! And by the way the dataset also provide a frequency count
of the word occurence in Google Books data. That can be useful to sort our list of candidate word
relevance. Perfect!

Since our script works, our solution is correct. I have implemented a web service for this
[http://four-pics-one-word-solver.appspot.com/][web-service] try it!


## Loading Data on App Engine

When I implemented the web service in Google App Engine, I tried to save the dataset
all the 97,566 in the datastore which turned out wrong! I hit the datastore free quota. And later
found out that it is much better to store it in memory and it will not accrue an API cost.
The way to do this is to store the list of words in the dataset into a data structure and searialize it (pickle).
Then later you can access the data at runtime by de-searializing it (unpickle). See below.

{% highlight python %}
#!/usr/bin/env python
import os
import pickle

FILEPATH = os.path.abspath('google-books-common-words.txt')
WORD_LENGTHS = []

def convert_to_dict(filename):
    data = {}
    with open(filename, 'r') as f:
        for line in f:
            if line.startswith('#'):
                continue
            key, value = line.split()
            data.update({key: int(value)})
        return data

def pickle_data(data, filename):
    if not isinstance(data, dict):
        raise TypeError('data should be a dict')
    with open(filename, 'wb') as f:
        pickle.dump(data, f, pickle.HIGHEST_PROTOCOL)
    print 'Data saved as: {}'.format(os.path.abspath(filename))

def unpickle_data(filename):
    data = None
    with open(filename, 'rb') as f:
        data = pickle.load(f)
    return data

if __name__ == "__main__":
    data = convert_to_dict(FILEPATH)
    pickle_data(data, 'google-books-common-words.bin')
    _data = unpickle_data('google-books-common-words.bin')
    print _data
{% endhighlight %}

Now go play 4 Pics 1 Word, and if your stuck you know where to go! :-)

[4pics1word]: https://play.google.com/store/apps/details?id=de.lotum.whatsinthefoto.us&hl=en
[mayzner]: http://norvig.com/mayzner.html
[web-service]: http://four-pics-one-word-solver.appspot.com/
