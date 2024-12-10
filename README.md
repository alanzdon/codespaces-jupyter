# French 379 Final Project - Improving NLP Pipelines for Classical French with Le Mercure Galant

Workbooks in order:
1. xml_processing
2. nlp_processing
3. pipeline_analysis

Machine learning can be used to help extract information and meaning from large volumes of text. In this instance, I wanted to try understanding attitutes and authorial intent in writings from the Mercure Galant, one of the first gazettes. In 2022, Steinberger wrote a significant amount on the impacts of its contents, specifically focusing on social norms, female readers, and the haziness between truth and fiction in its words. My initial hope was that I would be able to use language processing tools to explore the text and see if an algorithm would be able to separate propaganda and reality, for example.

This periodical has a rich history, but also a variety of archaic orthographic practices, which unfortunately lends itself poorly to the 'out-the-box' tools I might use to study it. Even for packages designed purposely for analysis of texts of this period, there weren't sufficient recources. Though the Classical Language Toolkit supports a variety of pipelines for many languages, only a tokenizer was available for Middle French. Essentially, all I could do with this package was to separate words and puncutation from each other.

spaCy, which is an open-source repository for natural-language processing, has support for modern French, but because the models are trained on modern French news sources, I could not fully trust its results at baseline. Given my limitations with the tools available for the task of understanding Le Mercure Galant, and not wanting to train a custom spaCy model, I decided to explore these pipelines and determine whether they could be suitable for a larger project with these works.

1. xml_processing.ipynb
There is a large corpus of text from the first nearly 40 years of the Mercure hosted on GitHub, which was supported by researchers at the University of Sorbonne. It is in XML form, which I began by parsing using the Beautiful Soup package. After separating the articles and extracting their bibliographic contents, titles, and body text, I combined all of this data into a pandas DataFrame to organize and further process the text.

2. nlp_processing
I then attempted to parse the text with two different models, which I believed could have varying amounts of success with the source material. 'fr-core-news-sm' is a small, highly efficient model with general function that is trained on a small amount of data. Despite its small scale, it is capable of tasks like Named Entity Recognition, which is useful for a variety of comprehension tasks. I wanted to compare this against 'fr-dep-news-trf', which is a dependency transformer with high accuracy developed specifically for analyzing how words in a sentence are connected (or dependent) on one another. However, this model is much less efficient than the small model-- this is acceptable for small pieces of data, but this practice can't be reasonably scaled to corpora much larger than Le Mercure Galant.

The small model (core) could parse the entire corpus in 2-3 minutes, while the larger dependency model (dep) could take over 100 minutes to run. My task for attempting nlp processing with this corpus was to see how much closer I could get the core model to achieve the same success as the dep model, and also to determine how accurate either of these models were at all.

I ran them both and compared the differences in their results, which isn't exactly a proxy for accuracy but was helpful nonetheless. Seeing how specific archaic orthographic practices affected the lemmatization and POS estimation features of the models, I decided to modify the original text to 'help' the parsers along. I identified three specific features which were 1) consistent across words with this feature and 2) would hopefully be simple modifications which could have significant impact on the parsing features of the models. The y -> i shift was the first for which I identified plenty of occurrences, mistakes in the lemmatization and POS results, and was relatively easy to shift. Next was the conjugation shift from -o- to -a-. Finally the sç->s shift, which was the simplest. For each of these transformations, I found the 100 most common tokens associated with them and tried manually to list words for which a modernization did not apply (i.e. ouy became oui, but ayant remained unchanged). Then I ran both models again and collected the data.

3. pipeline_analysis

Finally, it was time to compare my results. The strict number of differences between model results did not shift as much as I had hoped-- my thought was that the dep model was more forgiving of unfamiliar data, and that the modifications would allow the core model to catch up. However, more importantly, I had to estimate the accuracy levels for these tools and determine if my changes had an impact. I randomly selected just 5 articles for which to determine ground truth accuracies, and with Laela's help, we read through them and found whether the model was correct or incorrect for its lemmatization and POS estimate. We evaluated over 1000 tokens, but compared to the corpus' 5.5 million, the results are certainly less than definitive.

Given the breadth of analyses to make, I consulted ClaudeAI for assistance in creating visualizations which would highlight the most important impacts of the work I'd done.

Overall, the features of both models did show signs of improvement from the results-- especially the core lemmatizer. This was particularly evident when reading through verb results. The core model specifically struggled to parse verbs with archaic spellings. There's a chance it could guess sçait was a verb, but it could not match it with the true lemma, sçavoir, until ç was removed from the original text. 

The models tended to disagree strongly between proper nouns and common nouns, which is very important for features like Named Entity Recognition. The corpus contained words that were capitalized far more liberally than is done in modern French, and the core model had a tendency to interpret any capitalized word as a proper noun. Of the 'disagreements' between the models, this accounted for quite a significant portion. Unfortunately, distinguishing between common nouns and proper nouns was also a hard task for Laela and I to determine ground truths. In addition, there were not immediate orthographic modifications (like lowercasing all text) which would both assist the model and still preserve the text as much as possible.

There is still significant work to be done with Classical French natural language processing. There is an opportunity to train a custom spaCy model on Le Mercure text to improve its accuracy, but this necessitates that ground truths to be evaluated for a significant portion of the text. Other features, such as the morphologizer (which provides morphological analysis), named entities, entity links (connects named things to each other), and text similarity all rely on the results of the tokenizer, lemmatizer, and POS tagger. By improving the most basic steps used in NLP, we're one step closer to being able to make insights on Classical French text with the incredible tools afforded to other languages.

@article{richardson2007beautiful,
  title={Beautiful soup documentation},
  author={Richardson, Leonard},
  journal={April},
  year={2007}
}
 @article{Steinberger_2022, title={‘Fake News’ in Seventeenth-Century France: The Case of Le Mercure galant*}, volume={257}, ISSN={0031-2746}, DOI={10.1093/pastj/gtac032}, abstractNote={Le Mercure galant, one of France’s first newspapers, is notable for its diverse content: politics and foreign affairs, court news, science and medicine, the arts and literature. Directed by Jean Donneau de Visé from its inception in 1672 until his death in 1710, this influential and innovative monthly publication circulated throughout France and beyond its borders. The Mercure’s tendency to blur the lines between truth and fiction, between history and propaganda, and between information and entertainment, makes it an instructive case study in early modern ‘fake news’. Donneau de Visé, a self-styled royal historiographer and the beneficiary of a generous royal pension, dedicated his periodical to the Dauphin and published abundant praise of Louis XIV. The Mercure’s news reporting included distortions and propaganda intended to bolster and further entrench the king’s foreign and domestic policies. The Mercure’s nouvelles, short stories presented as true recent events, constituted another type of ‘fake news’, one that often had a different effect, inviting the re-examination of social norms. The nouvelles appealed to the Mercure’s sizeable community of women readers by accentuating female agency and providing a vehicle for the exploration of scenarios of female empowerment.}, number={Supplement_16}, journal={Past & Present}, author={Steinberger, Deborah}, year={2022}, month=nov, pages={143–171} }

 @article{CLERC_2020, title={De la mise en récit de l’histoire artistique au système d’information numérique partagé. Le programme Mercure galant}, url={https://sht.asso.fr/de-la-mise-en-recit-de-lhistoire-artistique-au-systeme-dinformation-numerique-partage-le-programme-mercure-galant/}, abstractNote={Le projet Mercure galant tient son nom d’un périodique du XVIIe siècle (1672-1710) qui fait figure de ressource essentielle pour l’histoire des spectacles. Développé dans un centre de recherche en musicologie du CNRS, il propose des éditions numériques en TEI et en MEI d’une large sélection de textes relatifs à la vie musicale, aux spectacles et à la littérature, ainsi que des musiques et des estampes publiées dans le périodique. Des opérations préliminaires à l’établissement d’un corpus textuel jusqu’à l’élaboration intellectuelle, scientifique et institutionnelle d’un système d’organisation des connaissances partagé via une plateforme numérique dédiée, cet article retrace les étapes qui ont guidé la démarche scientifique, en prenant comme fil conducteur la remise en question de la notion de donnée.}, journal={Société d’Histoire du Théâtre}, author={CLERC, Fabien}, year={2020}, month=sep, language={fr} }

 @software{NLTK :: Natural Language Toolkit, url={https://www.nltk.org/} }

@Article{         harris2020array,
 title         = {Array programming with {NumPy}},
 author        = {Charles R. Harris and K. Jarrod Millman and St{\'{e}}fan J.
                 van der Walt and Ralf Gommers and Pauli Virtanen and David
                 Cournapeau and Eric Wieser and Julian Taylor and Sebastian
                 Berg and Nathaniel J. Smith and Robert Kern and Matti Picus
                 and Stephan Hoyer and Marten H. van Kerkwijk and Matthew
                 Brett and Allan Haldane and Jaime Fern{\'{a}}ndez del
                 R{\'{i}}o and Mark Wiebe and Pearu Peterson and Pierre
                 G{\'{e}}rard-Marchant and Kevin Sheppard and Tyler Reddy and
                 Warren Weckesser and Hameer Abbasi and Christoph Gohlke and
                 Travis E. Oliphant},
 year          = {2020},
 month         = sep,
 journal       = {Nature},
 volume        = {585},
 number        = {7825},
 pages         = {357--362},
 doi           = {10.1038/s41586-020-2649-2},
 publisher     = {Springer Science and Business Media {LLC}},
 url           = {https://doi.org/10.1038/s41586-020-2649-2}
}

@software{reback2020pandas,
    author       = {The pandas development team},
    title        = {pandas-dev/pandas: Pandas},
    month        = feb,
    year         = 2020,
    publisher    = {Zenodo},
    version      = {latest},
    doi          = {10.5281/zenodo.3509134},
    url          = {https://doi.org/10.5281/zenodo.3509134}
}
