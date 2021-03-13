# Question
I currently have a json file I am manipulating with jq. I am able to display it how I want for the most part. However, I am running into a problem when displaying values for a field that's empty. I am using the // alternative operator but it is not returning the desired output. What is the right way to handle empty strings with jq?

Current Output:
```
RELEASE    INSTALLED  LATEST  DEPRECATED
test-app   1.0.0      2.0.0   false
test-app2  3.0.0      true
```
Desired Output:
```
RELEASE    INSTALLED  LATEST  DEPRECATED
test-app   1.0.0      2.0.0   false
test-app2  3.0.0      -        true
```
Jq:
```jq
cat test1.json | jq -r 'map({release, installed: .Installed.version, latest: (.Latest.version // "-"), deprecated}) | ( .[0] | keys_unsorted | map(ascii_upcase)), (.[] | [.[]]) | @tsv' | column -t
```
Json:
```json
[
  {
    "release": "test-app",
    "Installed": {
      "version": "1.0.0",
      "appVersion": ""
    },
    "Latest": {
      "version": "2.0.0",
      "appVersion": ""
    },
    "outdated": true,
    "deprecated": false
  },
  {
    "release": "test-app2",
    "Installed": {
      "version": "3.0.0",
      "appVersion": ""
    },
    "Latest": {
      "version": "",
      "appVersion": ""
    },
    "outdated": false,
    "deprecated": true
  }
]
```

# Answer
"" is not classed as null as Philippe pointed out, but if you're unable to change the json to make the "" become null, what you can do is

```jq
jq -r 'map({release, installed: .Installed.version, latest: (if .Latest.version == "" then "-" else .Latest.version end), deprecated}) | ( .[0] | keys_unsorted | map(ascii_upcase)), (.[] | [.[]]) | @tsv' data.json | column -t
```

# Original Link
https://stackoverflow.com/questions/66603976/jq-handling-empty-strings-and-replacing-them-with-a-default-value/66604770#66604770
