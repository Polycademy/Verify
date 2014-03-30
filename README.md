Verify
======

Verify is the best verification (validation) library that exists for PHP. Don't believe, just try it!

We will assume all data comes in the format of an array. This can come in any number of dimensions and any form of keys.

We will:

1. Firewall the data (elements)
2. Prefilter the data
3. Validate the data
4. Postfilter the ata

Multidimensional arrays demand that each stage allows full recursion. But also any specification can allow a sequence of data as well.

//key allows special conditions like 'sequence'	=> 0 (sequence starting from 0) or python like 'sequence' => '0:1' (half open range) sequence from 0 to 1 (including 0, but not including 1) also allows 'sequence' => 'a' or 'sequence' => 'A'
elements(array(
	'key' => 'value'
), $input_data)

//value points to the key on the first dimension
elements(array(
	'value'
), $input_data)

//value1 points to the key to the first dimension, then value 2 is the key on the second dimension
elements(array(
	'value1'	=> array(
		'value2'
	)
))

In most cases if data is not found in the firewall spec, it's left out. If data is found but not part of the spec, it's left out. But another property can allow it set data specified but not found to equate to null.
For example, for POST and PATCH, one should leave it out. But for PUT, one should make it equate to null, if you're following the RESTful standard of PUT being a full replacement update, but PATCH being a partial update. Meaning any data left out of full replacement update should be null.

One thing that filtering allows, is like boolean_style filtering. The library will be configurable during runtime but also during the instantiation allowing an IOC to pass in a standard set of filters and validation rules.

Another interesting issue is in the realm of the update. Sometimes when updating against a database record. You have to validate that a certain inputted field's value does not violate some condition according to another field that may or may not be inputted as well. If it's inputted, then it needs to be checked against that inputted field. If it's not, it needs to be checked against the database's field. This is for only partial updates. One could use anonymous functions here, because that data is not available on the inputted $data. The $extra_data could be used as stuff that you may need for the rule's anonymous functions to utilise.

$verified_data = $this->verify->validate(array(rules), $input_data, $extra_data);

$errors = $verified_data->errors;
if($errors){
	var_dump($errors);
	return false;
}

//continue
$data = $verified_data->content;

The data that has no relevant rules will be filtered out.

Ok Controllers sometimes needs to filter out data as well.
In the Dragoon framework. All request parameters would be part of a request object that is injected in the function or object.

```
[
	//sequence rules applies to special keys: alphabets (a, A) or digits (0, 1.. etc)
	//all rules plus all child rules applies to each item in the sequence
	'sequence'	=> [
		'key'	=> 'a',
		'label'	=> 'Alphabet Sequence',
		'rules'	=> [
			'MinLength:Blah',
			'!Regex:Blah'
			'NotEmpty', //means not null and not '' (empty string) (everything else is allowed).. this is not the same as !empty()...
			'NotFalsey',
			'BooleanStyle',
			'Binary', //0 or 1 not '0' or '1'
			'Exist',
		],
		'child'	=> [

		],
		'beforeFilters'	=> [
		],
		'afterFilters'	=> [
		],
		'filters'	=> [
		]
	],
	[
		'key'	=> 'title',
		'label'	=> 'Title',
		'rules'	=> [

		],
		'child'	=> [

		]
	]
]
```

https://github.com/bobthecow/Ruler
https://github.com/bobthecow/Ruler/issues/27#issuecomment-36432685

RPC clusterable Verification service. Will use Open Service Discovery Schema.
Remeber even though it's developed as a service, it's subcomponents can be used and composited into a new service. So it acts like a "library" and as a "service" depending on what you want. The entry point creates a service.

This thing could be CPU intensive but also IO intensive. So I suggest PHP AMP. As it's a daemon + multithreaded capability.
