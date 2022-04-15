Correlation Rules
=================

## Fields Reference

**name**

This is the name that we will use for the alerts of the current rule.

**severity**

The severity of the alerts for this rule. Use the majuscule initial. Possible values: Low, Medium, High.

**description**

The description that will appear in the alerts.

**solution**

If there is a known solution for this incident, specify here.

**category**

If there is any category in which the alert can be grouped.

**tactic**

If there is an incident detected by this rule fits into any of the attack tactics.

**Reference**

A list of URLs where you can get more information about the attack.

**frequency**

How often in seconds the alert should be checked.

**cache**

This field declares that the iterations will occur on the cache of the correlation engine and contains the definition of said iterations. When this field is used, the search field is not used and vice versa.

**cache -> allOff**

All comparisons within this field must be met for the rule to generate an alert.

**cache -> oneOff**

Any comparison within this field must be met for the rule to generate an alert.

**cache -> \[allOff | oneOff\] -> field**

The field on which the comparison will be applied.

A field is a series of keys separated by dots. It can contain special wildcard characters '\*' and '?'. To access a slice value, use the index as the key. To get the number of elements in a slice or to access a child path, use the '#' character. Dot and wildcard characters can be escaped with '\\'.

You can also query a slice for the first match using # (...), or find all matches with # (...) #. You can use the comparison operators (==,!=, <, <=,>,>=) and the simple pattern matching operators (% and !%).

This description also applies to \[cache | search\] -> save -> field

```
{
  "name": {"first": "Tom", "last": "Anderson"},
  "age":37,
  "children": ["Sara","Alex","Jack"],
  "fav.movie": "Deer Hunter",
  "friends": [
    {"first": "Dale", "last": "Murphy", "age": 44, "nets": ["ig", "fb", "tw"]},
    {"first": "Roger", "last": "Craig", "age": 68, "nets": ["fb", "tw"]},
    {"first": "Jane", "last": "Murphy", "age": 47, "nets": ["ig", "tw"]}
  ]
}

"name.last"                           >> "Anderson"
"age"                                 >> 37
"children"                            >> ["Sara","Alex","Jack"]
"children.#"                          >> 3
"children.1"                          >> "Alex"
"child*.2"                            >> "Jack"
"c?ildren.0"                          >> "Sara"
"fav\.movie"                          >> "Deer Hunter"
"friends.#.first"                     >> ["Dale","Roger","Jane"]
"friends.1.last"                      >> "Craig"
"friends.#(last=="Murphy").first"     >> "Dale"
"friends.#(last=="Murphy")#.first"    >> ["Dale","Jane"]
"friends.#(age>45)#.last"             >> ["Craig","Murphy"]
"friends.#(first%"D*").last"          >> "Murphy"
"friends.#(first!%"D*").last"         >> "Craig"
"friends.#(nets.#(=="fb"))#.first"    >> ["Dale","Roger"]
```

**cache -> \[allOff | oneOff\] -> operator**

Operator to use in the comparison. See information about the operators in [Operators](https://github.com/AtlasInsideCorp/UTMStackCorrelationRules/blob/master/README.md#operators)

**cache -> \[allOff | oneOff\] -> value**

Value to compare the content of "cache -> \[allOff | oneOff\] -> field". In the second iteration case or onwards, you can use an alias to use the content of that alias as a value.

**cache -> timeLapse**

How much time backward in seconds will be checked in the logs.

**\[cache | search\] -> minCount**

How many minimum logs must be obtained as a result for this rule to be met.

**\[cache | search\] -> save**

Required fields to save to use in the next cycle iteration or to complete the information of the alerts.

**\[cache | search\] -> save -> field**

The original name of the field to store.

**\[cache | search\] -> save -> alias**

The alias or name to access the field.

There cannot be two or more aliases with the same name within the same iteration.

In the last iteration, the system will use the following aliases to fill in the alert details:

*   Protocol
*   SourceUser
*   SourceHost
*   SourceIP
*   SourcePort
*   DestinationUser
*   DestinationHost
*   DestinationIP
*   DestinationPort

The geolocation will be filled automatically from the SourceIP and DestinationIP aliases.

If the SourceIP or DestinationIP aliases do not exist, the system will generate them using SourceHost and DestinationHost, and vice versa.

If any field is saved with the aliases AlertName and AlertCategory in the last iteration, the alert name or category will be overwritten with those aliases' content as appropriate.

**search**

This field declares that the iterations will occur on Elasticsearch and contains the said iterations definition. When this field is used, the cache field is not used, and vice versa.

**search -> query**

The elasticsearch or opensearch query in json format. Remember to enclose the query in simple quotation marks.

Operators

**\==**

It’s true if the field's content is exactly equal to “value” content. It is sensitive to capital letters.

*   hello == Hello //False
*   hello == hello //True

**::**

It's true if the field's content is equal to "value" content. It is not sensitive to capital letters.

*   hello :: Hello //True
*   hello :: hello //True

**!= y <>**

It's true if the field's content is unequal to "value" content. It is sensitive to capital letters.

*   hello != Hello //True
*   hello != hello //False

**!!**

It's true if the field's content is unequal to "value" content. It is not sensitive to capital letters.

*   hello !! Hello //False
*   hello !! hello //False

**contains**

It's true if the "value" content is part of the field content.

*   "hello world" contains "world" //True
*   "hello world" contains "worlds" //False

**not contain**

It's true if the "value" content is not part of the field content.

*   " hello world " not contain " world " //False
*   " hello world " not contain " worlds" //True

**in**

It's true if the field content is part of the "value" content.

*   " world " in "hello, world, this, is, a, test" //True
*   " worlds" in "hello, world, this, is, a, test" //False

**not in**

It's true if the field content is not part of the "value" content.

*   " world" not in "hello, world, this, is, a, test " //False
*   " worlds" not in "hello, world, this, is, a, test " //True

**start with**

It's true if the "value" content is a prefix of the field content.

*   " hello world " start with " world " //False
*   " hello world " start with "hello" //True

**not start with**

It's true if the "value" content is not a prefix of the field content.

*   " hello world " not start with " world " //True
*   " hello world " not start with " hello " //False

**end with**

It's true if the "value" content is a suffix of the field content.

*   " hello world " end with " world " //True
*   " hello world " end with "hello" //False

**not end with**

It's true if the "value" content is not a suffix of the field content.

*   " hello world " not end with " world " //False
*   " hello world " not end with " hello " //True

**regexp**

It's true if the field content matches the regular expression of "value".

*   "adam\[23\]" regexp "^\[a-z\]+\\\[\[0-9\]+\\\]$" //True
*   " hello world " regexp "^\[a-z\]+\\\[\[0-9\]+\\\]$" //False

**not regexp**

It's true if the field content does not match the regular expression of "value".

*   "adam\[23\]" not regexp "^\[a-z\]+\\\[\[0-9\]+\\\]$" //False
*   " hello world " not regexp "^\[a-z\]+\\\[\[0-9\]+\\\]$" //True

**exist**

It's true if the field exists. The value must leave empty, as it must exist, but it will not be used.

**not exist**

It's true if the field does not exist. The value must leave empty, as it must exist, but it will not be used.

**in cidr**

It's true if the IP in the field is within the value range.

**not in cidr**

It's true if the IP in the field is not within the value range.

#### **Classic mathematical operators that only apply to numbers. Use only when the field content and the "value" are numeric.**

*   <
*   \>
*   <=
*   \>=

#### **When to use cache or search**

We recommend using the cache for the rules that will analyze logs in a maximum of 1h.

We recommend using search when the analysis period exceeds 1h or the rule's complexity is very high, and there is no way to do it using the cache.

#### **Examples**

~~~
- name: Windows authentication failure
  severity: Low
  description: A Windows user was unable to authenticate with the server or workstation more than 5 times.
  solution: Refer to NIST guidelines when creating password policies and set account lockout policies after a certain number of failed login attempts to prevent passwords from being guessed. Too strict a policy may create a denial of service condition and render environments un-usable, with all accounts used in the brute force being locked-out.
  category: User Account Authentication
  tactic: "Brute Force: Password Guessing"
  reference:
    - "https://attack.mitre.org/techniques/T1110/001/"
  frequency: 10
  cache:
    - allOf:
        - field: logx.wineventlog.event_id
          operator: "=="
          value: 4625
      minCount: 1
      timeLapse: 15
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
    - allOf:
        - field: logx.wineventlog.event_id
          operator: "=="
          value: 4625
        - field: logx.wineventlog.event_data.TargetUserName
          operator: "=="
          value: "{{.DestinationUser}}"
        - field: logx.wineventlog.host.name
          operator: "=="
          value: "{{.DestinationHost}}"
      minCount: 5
      timeLapse: 60
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
        - field: logx.wineventlog.event_data.WorkstationName
          alias: SourceHost
        - field: logx.wineventlog.event_data.IpAddress
          alias: SourceIP
~~~

~~~
- name: Windows authentication failure
  severity: Low
  description: A Windows user was unable to authenticate with the server or workstation more than 5 times.
  solution: Refer to NIST guidelines when creating password policies and set account lockout policies after a certain number of failed login attempts to prevent passwords from being guessed. Too strict a policy may create a denial of service condition and render environments un-usable, with all accounts used in the brute force being locked-out.
  category: User Account Authentication
  tactic: "Brute Force: Password Guessing"
  reference: 
    - "https://attack.mitre.org/techniques/T1110/001/"
  frequency: 10
  search:
    - query: '{"size": 500, "query": {"bool": {"must": [{"match_phrase": {"logx.wineventlog.event_id": 4625}}], "filter": [{"range": {"@timestamp": {"gte": "now-15s", "lte": "now"}}}], "should": [], "must_not": []}}}'
      minCount: 1
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
    - query: '{"size": 500, "query": {"bool": {"must": [{"match_phrase": {"logx.wineventlog.event_id": 4625}}, {"match_phrase": {"logx.wineventlog.event_data.TargetUserName": "{{.DestinationUser}}"}}], "filter": [{"range": {"@timestamp": {"gte": "now-60s", "lte": "now"}}}], "should": [], "must_not": [{"match_phrase": {"logx.wineventlog.event_data.TargetUserName": "{{.DestinationHost}}$"}}]}}}'
      minCount: 5
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
        - field: logx.wineventlog.event_data.WorkstationName
          alias: SourceHost
        - field: logx.wineventlog.event_data.IpAddress
          alias: SourceIP
~~~
