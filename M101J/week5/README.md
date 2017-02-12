# Homework 5.1
_Use the aggregation framework to calculate the author with the greatest number of comments._

*Document structure*

```javascript
{
	"_id" : ObjectId("50ab0f8bbcf1bfe2536dc3f9"),
	"body" : "Amendment......",
	"permalink" : "aRjNnLZkJkTyspAIoRGe",
	"author" : "machine",
	"title" : "Bill of Rights",
	"tags" : [
		"watchmaker",
		"santa",
		"xylophone",
		"math",
		"handsaw",
		"dream",
		"undershirt",
		"dolphin",
		"tanker",
		"action"
	],
	"comments" : [
		{
			"body" : "Lorem .....",
			"email" : "HvizfYVx@pKvLaagH.com",
			"author" : "Santiago Dollins"
		},
		.
		.
		.,
		{
			"body" : "Lorem .....",
			"email" : "HvizfYVx@pKvLaagH.com",
			"author" : "Santiago Dollins"
		}
	]
}
```

`db.posts.aggregate([ 
{ $unwind:"$comments" }, 
{ $group: {"_id":"$comments.author", "comments":{$sum:1}} }, 
{ $sort:{"comments":-1} } ])`

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------

# Homework 5.2
_Please calculate the average population of cities in California (abbreviation CA) and New York (NY) (taken together) with populations over 25,000.
For this problem, assume that a city name that appears in more than one state represents two separate cities._

*Data structure*

```javascript
{
	"_id" : "06705",
	"city" : "WATERBURY",
	"loc" : [
		-72.996268,
		41.550328
	],
	"pop" : 25128,
	"state" : "CT"
}
```
`db.zips.aggregate([ 
{$match:{$or:[{"state":"CA"},{"state":"NY"}]}}, 
{$group:{"_id":{city:"$city","state":"$state"},pop: {$sum:"$pop"}}}, 
{$match:{"pop":{$gt:25000}}}, 
{$group:{"_id":null,"population":{$avg:"$pop"}}} 
])`

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------

# Homework 5.3
_There are documents for each student (student_id) across a variety of classes (class_id). Note that not all students in the same class have the same exact number of assessments. Some students have three homework assignments, etc.
Your task is to calculate the class with the best average student performance. This involves calculating an average for each student in each class of all non-quiz assessments and then averaging those numbers to get a class average. To be clear, each student's average includes only exams and homework grades. Don't include their quiz scores in the calculation.
What is the class_id which has the highest average student performance?_

*Data structure*

```javascript
{
	"_id" : ObjectId("50b59cd75bed76f46522c34e"),
	"student_id" : 0,
	"class_id" : 2,
	"scores" : [
		{
			"type" : "exam",
			"score" : 57.92947112575566
		},
		{
			"type" : "quiz",
			"score" : 21.24542588206755
		},
		{
			"type" : "homework",
			"score" : 68.1956781058743
		},
		{
			"type" : "homework",
			"score" : 67.95019716560351
		},
		{
			"type" : "homework",
			"score" : 18.81037253352722
		}
	]
}
```

_You need to group twice to solve this problem. You must figure out the GPA that each student has achieved in a class and then average those numbers to get a class average. After that, you just need to sort. The class with the lowest average is the class with class_id=2. Those students achieved a class average of 37.6_

`db.grades.aggregate([
	{$unwind: "$scores"},
	{$match: {"scores.type": {$ne: "quiz"}}},
	{$group: {_id:{"student":"$student_id", "class":"$class_id"}, "avg_score_by_student":{$avg:"$scores.score"}}},
	{$group: {_id:{"class":"$_id.class"}, "avg_class_score":{$avg:"$avg_score_by_student"}}},
	{$sort:{"avg_class_score":-1}},
	{$limit:1}
])`

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------

# Homework 5.4
_Calculate the number of people who live in a zip code in the US where the city starts with a digit._

*Data structure*

```javascript
{
	"_id" : "35005",
	"city" : "ADAMSVILLE",
	"loc" : [
		-86.959727,
		33.588437
	],
	"pop" : 10616,
	"state" : "AL"
}
```

`db.zips.aggregate([
    {$project: {"_id":0, "city":1, "pop":1}},
    {$match: {"city": /^\d.*/ }},
    {$group: {"_id":null, "population":{$sum:"$pop"}}}
])`



