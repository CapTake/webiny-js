Data extractor support several ways to extract data, which are demonstrated in the following examples.
      
### Sample data
```
const data = {
	"firstName": "John",
	"lastName": "Doe",
	"age": 30,
	"enabled": true,
	"company": {
		"name": "Webiny LTD",
		"city": "London"
	},
	"subscription": {
		"name": "Free",
		"price": 0,
		"commitment": {
			"expiresOn": "never",
			"startedOn": 2018,
			"enabled": true
		}
	},
	"simpleCollection": [
		{id: 1, name: "one"},
		{id: 2, name: "two"},
		{id: 3, name: "three"},
		{id: 4, name: "four"}
	],
	promised: new Promise(resolve => {
		setTimeout(() => {
			resolve(100);
		}, 5);
	})
};
```

### Simple extraction
```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "firstName,lastName,age,company");
```

This will return the following result:

```
{
    "firstName": "John",
    "lastName": "Doe",
    "age": 30,
    "company": {
        "name": "Webiny LTD",
        "city": "London"
    }
}
```

Notice how the company was returned completely with all nested keys. But we can also return only specific nested keys.

### Nested keys with dot notation
```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "firstName,lastName,age,company.city");
```

This will return the following result:

```
{
    "firstName": "John",
    "lastName": "Doe",
    "age": 30,
    "company": {
        "city": "London"
    }
}
```

Another example:
```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "subscription.name,subscription.price,subscription.commitment");
```

This will return the following result:

```
{
    "subscription": {
        "name": "Free",
        "price": 0,
        "commitment": {
            "expiresOn": "never",
            "startedOn": 2018,
            "enabled": true
        }
    }
}
```


### Nested keys with square brackets notation
From the previous example, listing keys using `subscription.name,subscription.price,subscription.commitment` can become tiring. Alternatively,
square brackets can be used.

```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "subscription[name,price,commitment]");
```

This will return the same as in previous example.

More advanced example:
```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "age,subscription[name,price,commitment[expiresOn,enabled]]");
```

This will return the following result:
```
{
    "age": 30,
    "subscription": {
        "name": "Free",
        "price": 0,
        "commitment": {
            "expiresOn": "never",
            "enabled": true
        }
    }
}
```

### Extracting arrays
Data extractor recognizes when a specified key is an array, in which case it will iterate and execute extraction over each item.
```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "simpleCollection[name]");
```
This will return the following result:
```
{
    simpleCollection: [
        {name: "one"},
        {name: "two"},
        
        {name: "three"},
        {name: "four"}
    ]
}
```

### Extracting promises
In case key in given data represents an unresolved promise, data extractor will make sure it is first resolved.
```
const extractor = require("webiny-data-extractor");
await extractor.get(data, "promised");
```
This will return the following result:
```
{
    promised: 100
}
```