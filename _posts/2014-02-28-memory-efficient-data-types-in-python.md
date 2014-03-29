---
layout: post
title: Memory efficient data types in Python
categories: [discovery]
tags: [python, numpy, beautiful soup, data scraping]
description: Discovering the different types of data storage with regards to memory efficiency.
---

A little bit of context for this topic and why I decided to write about it, I was doing some data scraping with (Beautiful Soup)[http://beautiful-soup-4.readthedocs.org/en/latest/#] where I wanted to extract data from a site's HTML and store it temporarily in memory before storing it in a database or on disk. This process involved submitting a form to the website, parse the response body which was in HTML, extract the relevant data, and repeat until the all different combination of inputs to the form has been exhausted. For what I was doing, each form submission gave me upwards of 100 data points and I have to do about 12000 form submission to extract all the data I want, which translates to roughly 120 000 data points. Normally this number is not something to worry about if I was concerned with how much memory I need to store these data, but I was experiencing memory problems and my computer started memory swapping before I finished extracting 80% of the data. Checking my Task Manager I found that python.exe was consuming more than 6GB of memory, of course my first thought was that a memory leak was happening because there is just no way 120000 strings of 5 characters would take up that much space.

Let me an example of what I was doing when I encountered this problem.

```python
import requests
from bs4 import BeautifulSoup

URL = "" # insert url of site to parse
form_fields = [{}, {}] # array of form inputs for simplicity's sake

# store extracted data here
data = []

for i in range(0, len(form_fields)):
	data[i] = []
	# variable that holds the HTML of the response body to be used for parsing
	soup = BeautifulSoup(requests.post(URL, data=form_fields[i]).text) 
	for cell in soup.find_all("td"):
		data[i].append(cell.contents[0]) # store table data for each for each form submission

# continue script
```

If we are familiar with languages like Java and we are just starting out with Python, the above code would look like we are just iterating through the different form inputs and saving the resulting data from the HTML of the response. And with each iteration, `soup` would just be replaced with a new response from which we could extract new data from. If we are used to working with statically-typed languages such as Java, we can just declare 

```java
ArrayList<String> data = new ArrayList<String>();
```

and we can be sure that `data` will only store String objects or else a runtime error occurs. But when we declare an empty list in Python that list can store any data type because Python is a dynamically-typed language, and in the code above `data` is actually storing the reference to the data that I want in the HTML rather than the data itself.  So what happens at every iteration of the loops is, each BeautifulSoup instance isn't freed because there still references pointing to it and the garbage collection cannot free the space. In other words, we are saving the whole HTML for each form submission, which explains  the memory leak.

So how do we solve this? Easy, just save the string copy of the data that we want to store (or any type we want for that matter). So we modify one line from the Python code above to

```python
data[i].append(str(cell.contents[0]))
```

and our memory leak problem is solved.

### More Memory Efficient Data Storage
So what if we want to hold even more data in memory? Probably something like 100 million data points? Are there more effective ways to hold these data in memory other than using the common Python data types like lists or tuples? Let me just say that upwards of 10000 data points, the difference between lists and tuples are quite negligible. Below that, tuples are more memory efficient that lists and of course there are little things we can do such as storing data as ints than strings and so on. But what we are looking for is something at least an order of magnitude more efficient.

And this is where [NumPy](http://www.numpy.org/) comes in, for storing numerical values it is as close as Python can get to a C style array where entries are stored in one contiguous memory block without references pointing everywhere to access an entry. Among the advantages of NumPy after trying it out briefly are:

- takes up half the space of Python lists when storing numbers.
- faster for performing mass operations on each entry.

but it is also without its cons:

- can be difficult to install. The steps to setting it up are very different for different systems. 
- less efficient if storing other data types such as strings
- not as many methods for modifying/extending the array once it is made since it behaves like a C array


That being said, I think everyone should give NumPy a try if they are dealing with large numbers of data given the massive advantages. So this post has been about me discovering the nuances of memory management in Python, and it is by no means meant to be thorough or detailed, it is only meant to describe the process of finding out what is actually going on. I'm sure there will be more of these to come. Till next time. 