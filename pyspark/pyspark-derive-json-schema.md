# Derive the json schema in pyspark 

I had to process a lot of deeply nested json files (compressed in bz2 format). Since these were large files, it was processed them using PySpark. 
It was working as expected and one day, it failed. It failed while run a SQL on the nested json. Upon analysis, it was found that there was data issue with the json
file. One of the child that was supposed to a nested json, turned out to be a string. 

As a solution to this problem, the schema needed to passed to the spark while reading the json. the question was how do
we get the schema? Well, its quite easy in PySpark 


```
# read all the json files and get the schema  
jsonDF = spark.read.json(list_of_files)
jsonSchema = jsonDF.schema.json()
open('schema.json', 'w').write(jsonSchema)

# you can store this schema in a text file and use it later 

# while reading the file 

schema = StructType.fromJson(json.loads(open('schema.json').read())
jsonDF = spark.read.schema(schema).json(list_of_files)

```
