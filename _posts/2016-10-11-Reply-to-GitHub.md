---
layout: post
title: Reply to GitHub
---


### [Notice](http://www.catb.org/~esr/faqs/smart-questions.html)

*	English is not my native language, please excuse my missplet or misuse.

*	I may need assistance translating this post. My email is `ljjdbd123@hotmail.com` and your help means a lot to me:)

*	Some slang expressions and idioms may be difficult for me...

<br/>

## Abstract

**It is important to use `TAB` instead of `SPACE` as indent, so that your markdown-content can be recognized and rendered correctly by kramdown.**

<br/>

##	Origin of this issue

A couple of days ago, I found that Jekyll(3.2.1), or kramdown(1.11.1) exactly, did not fully support the `syntax of markdown`, i.e., `code blocks` could not be rendered properly.

For example, the following markdown-content

```
* print hello in python for three times

	```python
	for i in range(3):
		print('hello')
	```
```

became

* print hello in python for three times

	```python
	for i in range(3):
		print('hello')
	```

And when I pushed this to `GitHub Pages`, it's still rendered like that. It's weird... because on my local markdown editor `MacDown` and on `GitHub--->Repo--->Create_New_File--->Preview`, it's something like this:

*	print hello in python for three times

	```python
	for i in range(3):
		print('hello')
	```

Note that the former block has been misunderstood

```
	```python 
	
```

and then the code has been mixed up in a single line.

<br/>

I've googled it, and didn't find much helpful information. I added this to my `_config.yml`:

```
kramdown:
 input: GFM
``` 

yet it didn't seem to help. Then I found `Redcarpet`, with which my code block was finally displayed correctly like below:

*	print hello in python for three times

	```python
	for i in range(3):
		print('hello')
	```

So I replaced `Kramdown` with `Redcarpet` and push it up to the `GitHub Pages`. But after that I received an email from `GitHub Support`, which said:


> The page build completed successfully, but returned the following warning:
> 
> You are currently using the 'redcarpet' Markdown engine, which is no longer supported by GitHub Pages and may cease working at any time. To ensure your site continues to build, remove the 'markdown' setting in your site's '_config.yml' file and confirm your site renders as expected. For more information, see https://help.github.com/articles/updating-your-markdown-processor-to-kramdown.
> 
> For information on troubleshooting Jekyll see:
> 
>   https://help.github.com/articles/troubleshooting-jekyll-builds
> 
> If you have any questions you can contact us by replying to this email.


I checked the two sites and it's something like that:

> GitHub Pages only supports kramdown as a Markdown processor...
> 
> ... so you can use Markdown with GitHub Pages the same way you use Markdown on GitHub...

As you can see, I used the `the same way you use Markdown on GitHub`, but my `code block` didn't work.  I updated Jekyll to 3.3.0 and kramdown to 1.12.0 and tried again, but the problem still exsisted. 

<br/>

## Solution

Well, about two days later, I accidently found a solution. It turns out that kramdown support only strict indent, otherwise `markdown-syntax` won't be rendered correctly.

As in the above example:

```
* print hello in python for three times
 ^ it's a [SPACE] here as indent
	```python
	for i in range(3):
		print('hello')
	```
^ it's a [TAB] here as indent
```

If I change the `*[SPACE]print ...` to `*[TAB]print`, and voila, the `code block` appears!

*	print hello in python for three times

	```python
	for i in range(3):
		print('hello')
	```

<br/>

The problem has been solved at last, though by accident. I've glanced over the content again in the two sites and, if I didn't miss anything, there was no hint for this problem. And because a paragraph starts with `*[TAB]blablabla` do works for its following code blocks on GitHub, I think there should be some kind of explaination for newbies like me:

> It is important to use [TAB] instead of [SPACE] as indent, so that your markdown-content can be recognized and rendered properly by kramdown.


