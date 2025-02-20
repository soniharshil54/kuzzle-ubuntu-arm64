---
code: true
type: page
title: getArray
description: KuzzleRequest class getArray() method
---

# getArray

<SinceBadge version="2.11.0" />

Gets a parameter from the request arguments and checks that it is an array.

<SinceBadge version="2.18.1" />

If the request has been made with the HTTP protocol and the request argument is not an Array but a JSON String
the argument will be parsed and returned if it's an array, otherwise an error will be thrown.

### Arguments

```ts
getArray (name: string, def: [] = null): any[]
```

</br>

| Name   | Type              | Description    |
|--------|-------------------|----------------|
| `name` | <pre>string</pre> | Parameter name |
| `def` | <pre>array</pre> | Default value to return if the parameter is not set |


### Example

```ts
const cities = request.getArray('cities');
// equivalent
const cities = request.input.args.cities;
//+ checks to make sure that "cities" is of the right type
// and throw standard API error when it's not the case
```
