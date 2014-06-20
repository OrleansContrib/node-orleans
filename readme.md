# node-orleans

A node.js client for Codename Orleans.

## Installation

Install the module with npm:

```
$ npm install node-orleans
```

Ensure the following are in the same directory as your node.js application:

* `Orleans.dll`
* Your grain interfaces dll
* `ClientConfiguration.xml`

## Initialising the client

First you need to initialise the client:

```js
var orleans = require('orleans');
var client = orleans({
	grainDll : "MyGrainInterfaces.dll", 	// the assembly containing your grain interfaces
	grainNamespace : "MyGrainInterfaces"	// the namespace of your grain interfaces
});

client.init(function(err){
	if (err) console.log(err);
	// we are ready to go!
});
```

## Calling a grain

The client has a `call` function which allows you to send a message to a grain:

```js
client.call(grainType, grainId, grainMethod, arguments, callback)
```
* `grainType` The name of your grain interface without the I (i.e. 'Grain1')
* `grainId` The ID of a grain. This can be either an `integer`, `string` (which will be parsed as a guid)  or an array with an extended primary key `[integer/string, string]`
* `grainMethod` The name of the method you want to call
* `arguments` An array of arguments
* `callback` A function which will be called back on completion of the request. For grains with no return value (i.e. they return `Task`) this should be `function(err)`, otherwise this should be `function(err, result)`


So for a grain interface that looks like this:

```cs
namespace MyGrainInterfaces
{
    public interface IGrain1 : Orleans.IGrain
    {
        Task SetValues(string stringValue, int intValue, bool boolValue);
        Task<string> GetString();
    }
}
```

We would use the following code to call it from node:

```js
// IGrain1.SetValues
client.call('Grain1', 0, 'SetValues', ['myString', 100, true], function(err){
	if (err) console.log(err);
});

// IGrain1.GetString
client.call('Grain1', 0, 'GetString', [], function(err, result){
	if (err) console.log(err);

	console.log(result); // 'myString'
});

```

## Known Issues

* Grains with generics are not supported (i.e. `IGrain<T>`)
* Only simple types are supported (i.e. `string`, `int`, `double`, `bool` );

## License 

MIT