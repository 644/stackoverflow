# Question
I'm new to jq and json schemas. I'm trying to figure out how to examine a schema and get back all $refs (specifically, their values) in to an array that I can examine in bash. I'm trying to get a list of $refs that are external to the file. Let's say I have a schema like this, how can I get an array of "Apple.schema.json" and "Banaan.schema.json" (or the entire propery would be fine)?

I'm using jq, and I'm struggling with knowing how to get a filter. I can look at properties via jq '.properties' [file], but beyond that, I'm not sure how to proceed.

```
{
    "$schema": "http://json-schema.org/draft-07/schema",
    "$id": "MySchema.schema.json",
    "version": "1.2.3",
    "type": "object",
    "properties": {
        "DEFINED_TYPE": { "type": "string" },
        "Apple": {"$ref": "Apple.schema.json"},
        "Banana": {"$ref": "Banana.schema.json"},
        "Chicken": {
            "$ref": "#/definitions/Chicken"
        },
        "TIME": {
            "type": "string",
            "format": "date-time"
        }
        
    }
}
```

# Answer
If I'm understanding you correctly, you want to read all $ref objects into an array in bash

So to get the list of $ref's
```
jq -r '.properties[] | select(."$ref" != null) | ."$ref"' filename
```
Then to add them to an array, use mapfile
```
mapfile -t array_name < <(jq -r '.properties[] | select(."$ref" != null) | ."$ref"' filename)
```
The -t option in mapfile will strip the trailing delimiter from each line read.

# Original Link
https://stackoverflow.com/questions/66532654/get-all-ref-values-if-any-from-a-json-schema/66533382#66533382
