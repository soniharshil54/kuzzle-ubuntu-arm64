---
code: true
type: page
title: getObject
description: KuzzleRequest class getObject() method
---

# getObject

<SinceBadge version="2.16.9" />

Gets a parameter from the request arguments and checks that it is an object.
We also support lodash syntax. [(`relations.lebron[0]`)](https://lodash.com/docs/4.17.15#get)

<SinceBadge version="2.18.1" />

If the request has been made with the HTTP protocol and the request argument is not an Object but is a JSON String
the argument will be parsed and returned if it's an oject, otherwise an error will be thrown.

### Arguments

```ts
getObject (name: string, def: JSONObject = null): JSONObject
```

</br>

| Name   | Type              | Description    |
|--------|-------------------|----------------|
| `name` | <pre>string</pre> | Parameter name |
| `def` | <pre>JSONObject</pre> | Default value to return if the parameter is not set |


### Example

```ts
const metadata = request.getObject('metadata');
// equivalent
const metadata = request.input.args.metadata;
//+ checks to make sure that "metadata" is of the right type
// and throw standard API error when it's not the case
```
