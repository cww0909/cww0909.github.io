---
layout: post
title: Serializing Python objects to JSON format
categories: [guide]
tags: [python, json, numpy]
description: How to encode custom Python classes to JSON format.
---

So for one reason or another you are looking to encode your Python classes into JSON format, the default Python [json](http://docs.python.org/3.3/library/json.html) library works quite well with all the default Python data types as shown [here](http://docs.python.org/3.3/library/json.html#py-to-json-table). But sometimes when we include a custom data type like a [NumPy](http://www.numpy.org/) array in our class, how should we address this problem then? Hopefully by the end of this post, I will have successfully explain how I did it and maybe it should point you in the right direction.

### Encoding Classes That Contain Common Data Types
So for example, the class you want to encode looks like this:
```python
class MyClass:
	def __init__(self, x ,y ,z):
		self.desc = "str"
		self.x = x
		self.arr = [y,z]
```
and you want to encode it this way:
```json
{
	"desc": "str"
	"x": "x"
	"arr": ["y", "z"]
}
```
Doing this is actually pretty simple, all you need to do is:
```python
import json

myInstance = MyClass(x,y,z)
json.dumps(myInstance.__dict__)
```
and the [json.dumps()](http://docs.python.org/3.3/library/json.html#json.dumps) will return a JSON formatted string of the class instance.

### Encoding Classes Containing 3rd Party Data Types
But what if you have a class that contains a 3rd party data type, such as a NumPy array? Or even less common data types such as [date](http://docs.python.org/3.3/library/datetime.html#date-objects)? What you can do is define another class that extends the (JSONEncoder)[http://docs.python.org/3.3/library/json.html#json.JSONEncoder] class and overwrite the (default())[http://docs.python.org/3.3/library/json.html#json.JSONEncoder.default] method to encode the data types that you want. Let us just dive into an example to see how this is done.

Now let's say we have a class like this:
```python
class MyClass:
	def __init__(self, id, date, someList):
		self.id = id # int or str
		self.date = date # date object
		self.data = array(someList) # NumPy array
```
If we call `json.dumps(myInstance.__dict__)` we would get a TypeError saying date is not serializable. So we need to define a custom JSON encoder, which would look like:
```python
class CustomEncoder(JSONEncoder):
	def default(self, obj):
		if isinstance(obj, date):
			return obj.__str__() # or define your own format here
		elif isinstance(obj, ndarray):
			return obj.tolist()
		elif isinstance(obj, MyClass):
			return obj.__dict__ # might as well define the encoding for our class
		return json.JSONEncoder.default(self, obj) # use the base class default() method otherwise
```
Note that in the custom encoder above, we can either convert the less common data types to a more common Python data type (such as int or str) that works with the base JSONEncoder class or define how the data should be represented in the format that we want. Then, we can just call `json.dumps(myInstance, cls=CustomEncoder)` and get:
```json
{
	"id": "1",
	"date": "2014-03-08",
	"data": [1, 2, 3]
}
```

Pretty neat, eh? So you can get the idea of how to do this with other data types, and hopefully it works as smoothly as it did for me.