---
output:
  pdf_document: default
  html_document: default
---
## The Books We Read

## Goal of the Project
The goal is two fold, exploring trends and patterns to understand how a book’s rating is affected by a user’s perception of it, using user-generated-tags. And then, predicting a book’s rating based on perceived knowledge

## Approach
This problem could be solved in two ways. Because ratings are continuous, I am going to explore the
following techniques:

- Regression with continuous average rating
	- Models used: Regression(with regularization)

- Classification with binned rating into ten levels(1-5 with steps of 0.5)
	- Models used: SVM, Random Forest

## Preliminary Observations
- The data set has a staggering 6M ratings and 34,400 tags for 10,000 most popular books along with ~53,400 users. The sheer number of ratings and tags would mean I’ll need to sample my data to support my Mac’s processing power.
- Before that, we’d need to clean the rating and tag datasets to both reduce noise and the data we can work with. Also, the book metadata has multiple ids that are not useful for the goal of this project that need to be dropped.
- The tags for the books include a wide range of labels from genres to to_reads to wish_list that need to be cleaned and reduced to relevant labels

## Exploratory Data Analysis
- The data set is fairly clean and needs minimal cleaning. Here are the steps before before the EDA process
- Drop irrelevant metadata from books(e.g. work_id, image_url, isbn)
- Remove tags that are not relevant, non-english and, that are small in number
- Sample 20 - 40 percentage of the user-rating dataset
- Create a column “genre” from tags that are common(e.g. crime, fantasy, fiction)
- Create columns for user intentions like “to-read” “to-buy” and “wish-list”

## Feature Generation and Multicollinearity
- There were common themes that could be categorized into their own columns like multiple tags for:
	- Intentions
	- Audiobooks
	- Genres
- These tags were consolidated and duplicate tags were dropped
- The correlation plot was very useful in identifying highly correlated tags that needed to be removed
- The end result was 52 columns(including 2 target columns)
- Target Columns
	- ‘average_rating’ used for regression
	- ‘average_rating_bin’ used for classification

## Findings
### Although user tags don’t significantly decide ratings, it’s clear from the analysis that tags, and an extension, user’s perceptions are impactful on how good the user feels the books are!

- Tags affecting ratings the most
	- All-time-favorites(positively)
	- Did-not-finish(negatively)
- Tags overall have an R2 value of .30 which seems appropriate


## About the Dataset

This dataset contains six million ratings for ten thousand most popular (with most ratings) books. There are also:

* books marked to read by the users
* book metadata (author, year, etc.) 
* tags/shelves/genres

## Access

Some of these files are quite large, so GitHub won't show their contents online. See [samples/](samples/) for smaller CSV snippets.

Open the [notebook](quick_look.ipynb) for a quick look at the data. Download individual zipped files from [releases](https://github.com/zygmuntz/goodbooks-10k/releases).

The dataset is accessible from [Spotlight](https://maciejkula.github.io/spotlight/datasets/goodbooks.html), recommender software based on PyTorch.

## Contents

**ratings.csv** contains ratings sorted by time. It is 69MB and looks like that:

	user_id,book_id,rating
	1,258,5
	2,4081,4
	2,260,5
	2,9296,5
	2,2318,3
	
Ratings go from one to five. Both book IDs and user IDs are contiguous. For books, they are 1-10000, for users, 1-53424. 	

**to_read.csv** provides IDs of the books marked "to read" by each user, as _user_id,book_id_ pairs, sorted by time. There are close to a million pairs.

**books.csv** has metadata for each book (goodreads IDs, authors, title, average rating, etc.). The metadata have been extracted from goodreads XML files, available in `books_xml`.

### Tags

**book_tags.csv** contains tags/shelves/genres assigned by users to books. Tags in this file are represented by their IDs. They are sorted by _goodreads_book_id_ ascending and _count_ descending. 

In raw XML files, tags look like this:

	<popular_shelves>
		<shelf name="science-fiction" count="833"/>
		<shelf name="fantasy" count="543"/>
		<shelf name="sci-fi" count="542"/>
		...
		<shelf name="for-fun" count="8"/>
		<shelf name="all-time-favorites" count="8"/>
		<shelf name="science-fiction-and-fantasy" count="7"/>	
	</popular_shelves>

Here, each tag/shelf is given an ID. **tags.csv** translates tag IDs to names.

### goodreads IDs

Each book may have many editions.  _goodreads_book_id_ and _best_book_id_ generally point to the most popular edition of a given book, while goodreads  _work_id_ refers to the book in the abstract sense. 

You can use the goodreads book and work IDs to create URLs as follows:

https://www.goodreads.com/book/show/2767052   
https://www.goodreads.com/work/editions/2792775  

Note that _book_id_ in **ratings.csv** and **to_read.csv** maps to _work_id_, not to _goodreads_book_id_, meaning that ratings for different editions are aggregated.
