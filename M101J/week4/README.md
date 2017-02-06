# Homework 4.3


`db.posts.explain("executionStats").find({permalink: "mxwnnnqaflufnqwlekfd"})`
`"winningPlan" : {
			"stage" : "COLLSCAN",
			...
			},
"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 1,
		"executionTimeMillis" : 1,
		"totalKeysExamined" : 0,
		"totalDocsExamined" : 1000,
		...
		}`

`db.posts.createIndex({permalink:1})`

`db.posts.explain("executionStats").find({permalink: "mxwnnnqaflufnqwlekfd"})`
`"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
			...
			},
"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 1,
		"executionTimeMillis" : 0,
		"totalKeysExamined" : 1,
		"totalDocsExamined" : 1,
		...
		}`

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------

`db.posts.explain("executionStats").find().sort({date:-1}).limit(10)`
`	"winningPlan" : {
			"stage" : "SORT",
			...
			}
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 10,
		"executionTimeMillis" : 6,
		"totalKeysExamined" : 0,
		"totalDocsExamined" : 1000,
		...
		}`

`db.posts.createIndex({date:-1})`

`db.posts.explain("executionStats").find().sort({date:-1}).limit(10)`
`"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 10,
		"executionTimeMillis" : 0,
		"totalKeysExamined" : 10,
		"totalDocsExamined" : 10,
		...
		}`

----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------

`db.posts.explain("executionStats").find({tags: "crab"}).sort({date:-1}).limit(10)`
`	"winningPlan" : {
			"stage" : "LIMIT",
			"limitAmount" : 4,
			"inputStage" : {
				"stage" : "FETCH",
				"filter" : {
					"tags" : {
						"$eq" : "crab"
					}
				},
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"date" : -1
					},
					"indexName" : "date_-1",
					"isMultiKey" : false,
					"direction" : "forward",
					"indexBounds" : {
						"date" : [
							"[MaxKey, MinKey]"
						]
					}
				}
			}
		},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 6,
		"executionTimeMillis" : 7,
		"totalKeysExamined" : 1000,
		"totalDocsExamined" : 1000,
		...
		}`

`db.posts.createIndex({tags:1})`

`db.posts.explain("executionStats").find({tags: "crab"}).sort({date:-1}).limit(10)`
`	"winningPlan" : {
			"stage" : "SORT",
			"sortPattern" : {
				"date" : -1
			},
			"limitAmount" : 10,
			"inputStage" : {
				"stage" : "KEEP_MUTATIONS",
				"inputStage" : {
					"stage" : "FETCH",
					"inputStage" : {
						"stage" : "IXSCAN",
						"keyPattern" : {
							"tags" : 1
						},
						"indexName" : "tags_1",
						"isMultiKey" : true,
						"direction" : "forward",
						"indexBounds" : {
							"tags" : [
								"[\"crab\", \"crab\"]"
							]
						}
					}
				}
			}
		},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 6,
		"executionTimeMillis" : 0,
		"totalKeysExamined" : 6,
		"totalDocsExamined" : 6,
		...
		}`

# Homework 4.4
_query the profile data, looking for all queries to the students collection in the database school2, sorted in order of decreasing latency. What is the latency of the longest running operation to the collection, in milliseconds?_
`db.sysprofile.find({ns:"school2.students"}, {millis:1}).sort({millis:-1})`
`{ "_id" : ObjectId("58988764b45d74020b4625a3"), "millis" : 15820 }
{ "_id" : ObjectId("58988764b45d74020b46221b"), "millis" : 12560 }
{ "_id" : ObjectId("58988764b45d74020b46237f"), "millis" : 11084 }
{ "_id" : ObjectId("58988764b45d74020b462232"), "millis" : 9493 }
{ "_id" : ObjectId("58988764b45d74020b462592"), "millis" : 9059 }
{ "_id" : ObjectId("58988764b45d74020b46223a"), "millis" : 7355 }
{ "_id" : ObjectId("58988764b45d74020b4625a7"), "millis" : 6765 }
{ "_id" : ObjectId("58988764b45d74020b462225"), "millis" : 6639 }
{ "_id" : ObjectId("58988764b45d74020b46238c"), "millis" : 6361 }
{ "_id" : ObjectId("58988764b45d74020b462464"), "millis" : 6310 }
{ "_id" : ObjectId("58988764b45d74020b46225b"), "millis" : 6236 }
{ "_id" : ObjectId("58988764b45d74020b46235f"), "millis" : 6231 }
{ "_id" : ObjectId("58988764b45d74020b46265a"), "millis" : 5858 }
{ "_id" : ObjectId("58988764b45d74020b46256d"), "millis" : 5816 }
{ "_id" : ObjectId("58988764b45d74020b4622ef"), "millis" : 5801 }
{ "_id" : ObjectId("58988764b45d74020b4623da"), "millis" : 5720 }
{ "_id" : ObjectId("58988764b45d74020b4625c9"), "millis" : 5704 }
{ "_id" : ObjectId("58988764b45d74020b4623a4"), "millis" : 5699 }
{ "_id" : ObjectId("58988764b45d74020b4624e7"), "millis" : 5666 }
{ "_id" : ObjectId("58988764b45d74020b46231c"), "millis" : 5587 }`


