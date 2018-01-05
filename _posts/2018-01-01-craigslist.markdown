---
layout: single
title: "Exploring Craigslist Musicians Communities"
category: Projects
---

In addition to data science, my other passion is playing music with other people. When I would travel around to different clients as a consultant, I usually found myself in new cities yearning to play with other musicians to learn new guitar techniques, cover my favorite songs, or just jam and create original material. In order to connect with other musicians who shared these interests, I relied heavily on responding to the 'musicians' posts on [Craigslist]. After years of using this service I have found that people post for variety of reasons, and sometimes the posts stray away from the express purpose of connecting musicians to each other, e.g. posts about selling gear, studios for rent, a critique of a newly released album. For me, avoiding these posts was fairly easy by using keyword searches. If I wanted to find somebody who wanted to play aggressive and hook-driven punk music, I could just enter searches for 'Ramones', 'Ty Segall', and 'Green Day', and usually I will find someone who has listed one or more of those bands as influences.  

However, when I was embarking on my fourth project during the [Metis] data science boot camp, I wanted to use Craigslist to reflect what musicians communities in aggregate. Instead of just find people with my specific interests, I was more interested in analyzing the posts' text to uncover sub-communities of Craigslist 'musicians' users to learn more about the posters' motivations for putting out ads, where these different motivations cluster in major U.S. cities, and perhaps learn more about these posters' tendencies. In order to accomplish these tasks I decided to use topic modeling to categorize the types of posts on Craigslist, and then build a [Flask application] to visualize how these categories change across different cities.

### Data Acquisition
After getting some test data from [https://newyork.craigslist.org/search/muc](https://newyork.craigslist.org/search/muc), I learned that to scrape every post on every Craigslist musicians board is very time-consuming due to the volume of posts. Because I had a week and a half until I had to present my findings and my web app to my Metis cohort, I decided I would limit the scope of my data to only those sites which had over 300 posts at the time I scraped them. I created a Scrapy spider to crawl [https://www.craigslist.org/about/sites], to find the 45 musicians sites that met this requirement, and then scraped those each post on each of the sites for the following information:  

- post title text
- post body text
- url of the post (sites on Craigslist are organized by geographic area)
- latitude and logitude of the post (if the post was geo-tagged)

### Cleaning and Preprocessing
Upon my first acquisition of the Craigslist data frim the New York site, I decided to do some broad cleansing of the text in each post:  
- removing post metadata created by Craigslist
- removing urls
- removing stopwords from the NLTK.corpus module
- Applying NLTK's implementation of a Porter Stemmer (stemming breaks down words to their root in order to make uniform different words with semantic similarities, e.g. *worked* and *working* are transformed to *work*). 

In order to feed this data into a machine learning model, I transformed the cleaned text into a TF-IDF matrix via scikit-learn's implementation of TfidfVectorizer using uni-grams and bi-grams. TF-IDF stands for "term frequency, inverse document frequency" and is a commonly used method to vectorize raw text based on the count of the terms in a text document divided by the number of documents in which the term appears in the entire text corpus. The idea here is to keep track of word counts in a given Craigslist post that describe the content of the post (e.g. bassist, touring, electric) while penalizing those words which appear too frequently across all posts and do not necessarily give us information about a singular post (e.g. music, search, rhythm). 'Uni-grams' and 'bi-grams' refer to the tokenization of terms into one-word and two-word phrases. These 'grams' capture more contextual information about our text (*professional vocalist* versus *novice vocalist*). My TF-IDF matrixp can be thought of a table which consists of ~20,000 rows (each representing a Craigslist post) and ~600,000 columns each representing a unique uni-gram or bi-gram. The values of this table correspond to a given term's TF-IDF score in a given post.
I wanted to see if I could extract any meaningful topics out of my corpus using Latent Dirichlet Allocation (LDA). The LDA algorithm is able  



[Flask application]: https://18.218.10.181:5000
[Craigslist]: https://craigslist.com
[Metis]: https://thisismetis.com