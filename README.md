# funcomp

Function composition is simple. However I didn't find any package for this so I wrote this lightweight library which works on Python 2 & 3.

### How to install?

You can use `easy_install` or `pip` to install the package.

	$ easy_install funcomp
	
### Example

##### 1. Simple function composition

We'll convert a roman number to integer and only keep numbers between 1 and 23. (Budapest's districts)
	
	>>> import roman
	>>> from funcomp import Composition, absorb
	>>> 
	>>> def remove_not_valid(district):
	...     return None \
	...         if district > 23 or district < 1 \
	...         else district
	... 
	>>> get_district = Composition(str, str.strip, roman.fromRoman, \
			int, remove_not_valid)
	
You can see it works like magic.

	>>> get_district('  XXI ')
	21
	
	>>> get_district('XXVI')
	None

##### 2. Absorb on error

Continue to investigate the previous example. If we give an integer to the compositor it returns an error.

	>>> get_district(9)
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	  File "funcomp/__init__.py", line 60, in __call__
	    return reduce(lambda value, func: func(value), self.functions, value)
	  File "funcomp/__init__.py", line 60, in <lambda>
	    return reduce(lambda value, func: func(value), self.functions, value)
	  File "build/bdist.macosx-10.10-intel/egg/roman.py", line 72, in fromRoman
	roman.InvalidRomanNumeralError: Invalid Roman numeral: 9

You can use the `absorb` method to ignore an error. It returns `None` when an error occur.

	>>> absorb(roman.fromRoman)(9)
	None
	
... or you can use the `use_original` attribute to retrieve the input value.
	
	>>> absorb(roman.fromRoman, use_original=True)(9)
	9	

Regarding of this, we can use the following composition.


	>>> get_district = Composition(str, str.strip, \
			absorb(roman.fromRoman, use_original=True), int, remove_not_valid)
		
	>>> get_district('XXI')
	21
	>>> get_district(' 9 ')
	9
	>>> get_district(54)
	None
	
### Notes

If you still don't know where you can use this package please take a look at [daprot](https://github.com/bfaludi/daprot) and [mETL transforms](https://github.com/ceumicrodata/mETL#transforms).

Enjoy!