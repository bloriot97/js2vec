# Js2Vec

I have tried to replicate the experiment based on [this](https://gab41.lab41.org/python2vec-word-embeddings-for-source-code-3d14d030fe8f) article from Alex Gude.

## Input data

I have gathered the js code I will be using from 3 major sources:
- My personal js files from different projects
- Some web brother libraries (Bootstrap, JQuery, P5.js...)
- Some node.js libraries

Then I had to remove the comments (not all of them, especially the comments from p5.js, because they contain some relevant js examples).

Then I have spaced a non-exhaustive list of punctuation mark from the text (. , ( ) [ ] etc.) and removed useless spaces from the file using regex.

The file would need some more filtering but here I am just building a small proof-of-concept.

## Word2Vec

I used the World2Vec library from [Apache Spark MLlib](https://spark.apache.org) with the following parameters [^1] :
```
model = Word2Vec()
    .setVectorSize(50)
    .setMinCount(10)
```

You can find the nootbook [here](/js2vec.ipynb).


## Results 
 
The model is working well in some cases. For example, with "i++" as input we get the following result:
 > ['j++', 'k++', 'length;', 'count;', '++i']
 
which are closely linked to for-loops. "split" also gives us some relevant words
> ['words,', 'fromCharCode', "'$1'", 'cars[ii]', 'trim']

These functions are closely related with string manipulation. "Promise" also suggests "resolve" or "error" which are typical var names for promises.

The array operator (=>) is used tu define a function, so it can be used with many possible arguments.
> ['fvarOffset', 'gposOffset', 'enableChoice', 'age', 'useNextTick']

We clearly see that those names corresponds to variables, however they donnot have anything specific to javascript, with a different input file we could have optained some totaly different sugestions. 

However, the dictionary must have been parasitized by some English sentences. As we see for example, the synonyms of "for" are :
> ['of', 'and', 'with', 'is', 'that']

Even though is() is a function it is not very likely to find it nearby a "for", "that" is not a keyword, and "with" is depreciated. This must be caused by some comments, or maybe by some strings in the code which does not respond to actual js code. 

The punctuation is also acting as a parasite; for example, when we are looking for synonyms of "function" we get 
> ['.', ';', '[', '(', ''] 

when we should probably get "=", "new" or some typical function names.  Punctuation should be considered separately from words. 

### Inherent problems with javascript

Nowadays javascript is used for a very wide variety of usage; codding some animations for a website is very different than making a small videogame or programing a server-side client with many asynchronous tasks. Even though the context is considered to create the model it is not used when the model is looking for synonyms thus it cannot always make relevant predictions. 

Moreover, javascript has a very free syntax thus it can vary a lot depending on the user, this should be considered. 

## Possible improvement

- Take the code from minified js files. Even though we would need to space the text there are no comments or other unnecessary words.
- Consider the context for suggesting the words and not only during the creation of the vectors.
- ...

[^1]: These parameters are based on the parameters used in the past-mentioned blog post they may be improved to fit more to the js code.
