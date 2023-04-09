# Task
This repository contains the code and the output file for the pre-req task performed for the LITES and ADRD project.

## Dataset 
We have the original dataset as 'mtsamples.csv'. 
1) It contains the following columns -> Description,	Keyword,	Medical_specialty,	Sample_name,	Transcription
2) There are no duplicates/nan/missing values in the dataset

## Task
The task was to extract the following entities:

### Age

I used regex for the same. I scanned through the dataset and picked out all sorts of cases. I believe a better way of doing this task would be to train a custom NER tagger using Spacy. We could provide a few initial annotations and then train the model in an incremental manner (i.e train the model using a few instances at a time and based on the feedback/error keep updating the model until a desired accuracy is obtained) since we have less data. I sugest this method as there are several ways of writing age of a person, like 2 years old, 2 years of age, 2-year-old, 2-1/2 year old, etc. It is difficult to cover all cases without avoiding repetations, false negatives and false positives. Deep learning methods can be used but they take more time to train and are heavy models in general. I have tried to cover most of the cases but I will also talk about the cases (to the best of my knowledge) I haven't been able to cover.

I used good old Regex for this task.

Cases covered:
1) 1 year(s) old,  1-year(s)-old,  1-year(s) old (similar for months, weeks, days)
2) 1 year(s) of age  (similar for months, weeks, days)
3) middle-aged, middle aged, middle-ages, young age, old age, gestational age
4) 2-1/2-years-old, 2-1/2-years old (leads to some issues, discussed later)


Edge cases:
1) 'A 1-month-26-day-old'  - predicted: 26 day old
2) 'at 3 days of age and then again at 3-1/2 years of age' - predicted: 3 days of age,  2 days of age
3) '51-year-old woman with 50-year-old sister' predicted: 51-year-old , 50-year-old . Here I need to use coreference resolution to detect the age associated with the patient.
4) '1+ year, black female' predicted: none


### Gender 
I did this task in 2 parts.

1) First I defined a dictionary of all the words that tell us about the gender of a person for Male: ['male', 'man', 'boy', 'gentleman', 'Male', 'Man', 'Boy', 'Gentleman'] and for Female: ['Woman', 'Female', 'woman', 'female', Lady', 'lady', 'Girl', 'girl'] (both upper and lower case)
I searched for these strings using simple string matching, covering some border cases like, if the character ',' or '.' occurs before or after the word.

2) Second I used the POS tagger to identify sentences that had no occurrences of the above mentioned words. I wanted to identify the words 'he', 'she', 'his' and 'her'(including upper case as well). I parsed the sentence through a POS tagger (NLTK) and then identified the tags 'PRP' and 'PRP$'. 'PRP' tag is able to identify 'he' and 'she'. 'PRP$' is able to handle 'his' and 'her'. 

### Treatment
The task was to extract the treatment administered to the patient using the 'Desription' column.
To extract the 'treatment' I used the  distilbert-base-cased-distilled-squad Q/A model. (https://huggingface.co/tasks/question-answering). I provided the description as the context and my question was 'What is the treatment administered to the patient?'. The model seems to have a considerable accuracy. Better answers can be produced by fine tuning the model on domain specific medical data or providing more context (append keywords?). Also, since I used a deep learning model, the method took a while to produce outputs.


