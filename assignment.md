# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: From the `movies` collection, return the documents with the `plot` that starts with `"war"` in acending order of released date, print only title, plot and released fields. Limit the result to 5.

Answer:

```python
# import pymongo package
import pymongo

# Access a database using attribute style access
db = client.sample_mflix
# Assign the `movies` collection to a variable
movies = db.movies

# using for loop to print out the result
for m in movies.find(
    {"plot": {"$regex": "^war"}}).sort("released", pymongo.ASCENDING).limit(5):                                             
    print(f"{m['title']} with plot '{m['plot']}' was released on {m['released']}")
```

### Question 2

Question: Group by `rated` and count the number of movies in each.

Answer:

```python
# $group: It groups documents (movies) based on some field
# "_id": "$year": Group the movies by their year field
stage_group_rated = {
   "$group": {
         "_id": "$rated",
         # Count the number of movies in the group:
         "movie_count": { "$sum": 1 }, 
   }
}

pipeline = [
   stage_group_rated,
]
results = movies.aggregate(pipeline)

# Loop through the 'results' documents:
for result in results:
   print(result)
```

### Question 3

Question: Count the number of movies with 3 comments or more.

Answer:

```python
# $lookup: attach comments to each movie
stage_lookup_comments = {
   "$lookup": {
         "from": "comments",
         "localField": "_id",
         "foreignField": "movie_id",
         "as": "related_comments",
         }
}

# $addFields: It creates a new field in the document, counts comments per movie
stage_add_comment_count = {
   "$addFields": {
         "comment_count": {
            "$size": "$related_comments"
         }
   } 
}

# $match: keeps only movies with 3 or more comments
stage_match_with_comments = {
   "$match": {
         "comment_count": {
            "$gte": 3
         }
   } 
}

# $group: count how many such movies there are
stage_group_count = {
   "$group": {
         "_id": None,
         "movie_count": {"$sum":1},
   }
}

# combine stages into 1 pipeline
pipeline = [
   stage_lookup_comments,
   stage_add_comment_count,
   stage_match_with_comments,
   stage_group_count,
]

results = movies.aggregate(pipeline)
for result in results:
   print(result)
```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
