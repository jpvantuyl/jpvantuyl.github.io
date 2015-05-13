---
layout: post
title: "About Powershell Koans"
date: 2015-05-13 -0800
comments: true
categories: [powershell, testing]
---

I've started a small hobby project inspired by my favorite code koans.  I first ran into the [Ruby koans](http://rubykoans.com/) years back and have found them a fun and accessible way to describe a language.  I've enjoyed my walk through the Javascript koans ([mrdavidlaing](https://github.com/mrdavidlaing/javascript-koans) and [liammclennan](https://github.com/liammclennan/JavaScript-Koans) versions) and started in on the [Python koans](https://github.com/gregmalcolm/python_koans) by Greg Malcolm.  So when I wanted to know more about Powershell I searched Github for a set of koans to walk me through the features and syntax of the language.  To my disappointment I didn't find anything.  So I decided to write my own.

The challenge of course is finishing them.  I've found inspiration in the structure and content of Greg Malcom's work.  He wrote the original Python koans and is also a primary maintainer of the mrdavidlaing version of the JS koans.  Stepping through the code from a producer side instead of a consumer has given me an appreciation for how to structure lessons that teach themselves.

The framework that is not a framework
-------

A koans project is easiest to stand up when leveraging a nice unit testing framework.  In Powershell's case that's clearly [Pester](https://github.com/pester/Pester).  It provides BDD style should syntax and chains together very fluently.

	Describe "AboutAsserts" {
		It "should expect true" {
			# We shall contemplate truth by testing reality, via asserts.
			$true | Should Be $false # This should be true
		}
	}

In this example, we're piping `$true` to the `Should` function which then asserts equality using the `Be` function.  The thing is, you don't need to know how the functions are implemented or much of anything about Powershell to get started.  Even for a novice, the syntax has sufficient context to lead you to the correct solution.

![powershell koans](https://johnvantuyl.com/images/content/2015/05/powershell-koans-solutions.png)

Deeper down the rabbit hole
-------

All code koans have a few things in common

 - An entry point to run the tests
 - Report all test successes
 - Stop running tests at the first failure
 - Report which test failed with a helpful stack trace
 - Add a nice zen saying somewhere

The Python koans split each of these out into a complex set of modules.  I started down that path but reversed course and simplified down to a single file.  Powershell makes it easy to use the objects exported by Pester and pretty up the output using the native `Write-Host` function.  This is my first stable version:

	$ScriptDir = Split-Path -parent $MyInvocation.MyCommand.Path
	Import-Module $ScriptDir\..\lib\Pester
	
	
	#helpful defaults
	$__FILL_ME_IN__ = "FILL ME IN"
	
	
	#run koans, results ordered by file name then by order within file
	$allKoans = Invoke-Pester -PassThru -Quiet
	
	
	#output results
	$about = ""
	$karma = $true
	$i = 0
	While ($karma) {
		$koan = $allKoans.TestResult[$i]
		
		if ($about -ne $koan.Describe) {
			$about = $koan.Describe
			Write-Host "Thinking $about" -ForegroundColor Magenta
		}
		
		$name = $koan.Name
		
		if ($koan.Passed) {
			Write-Host "    $name has expanded your awareness." -ForegroundColor Green
		} else {
			$failed = $koan.FailureMessage
			$stackTrace = $koan.StackTrace
			
			Write-Host "    $name has damaged your karma." -ForegroundColor Red
			Write-Host ""
			Write-Host "You have not yet reached enlightenment ..."
			Foreach ($str in $failed -split "\n") {
				Write-Host "    $str" -ForegroundColor Red 
			}  
			Write-Host ""
			Write-Host "Please meditate on the following code:"
			Foreach ($str in $stackTrace -split "\n") {
				Write-Host "    $str" -ForegroundColor Yellow
			}
			Write-Host ""
			Write-Host ""
		}
		
		$i += 1
		$karma = $koan.Passed -and $i -lt $allKoans.TestResult.Length
	}
	Write-Host "Flat is better than nested." -ForegroundColor Cyan

Pretty straightforward, eh?  Next steps would be to add more [cryptic zen sayings](https://github.com/jpvantuyl/python_koans/blob/master/python2/runner/sensei.py) to the last Cyan bit.  That and build out more koans.  And maybe create an automated build on [appveyor.com](http://appveyor.com)
