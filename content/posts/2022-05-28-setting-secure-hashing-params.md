---
author: 'Rahul Zhade'
date: 2022-05-28
linktitle: Setting Secure Hashing Parameters for Password Hashing
title: Setting Secure Hashing Parameters for Password Hashing
weight: 10
tags: ['security', 'hashing']
---

> TLDR: As of May 2022, I’d recommend using Argon2Id with the parameters recommended in [RFC 9106](https://www.rfc-editor.org/rfc/rfc9106.html)  

When trying to select the right way to protect your user’s passwords and credentials, there’s two things we need to consider. Firstly, we need to choose the right algorithm, one that is cryptographically secure. Secondly, we need to se the right _parameters_ for the given algorithm to make it behave as securely as possible. 

Secure behavior for a hashing function means that, in addition to being cryptographically secure, it should also be as resource intensive as possible. This will help prevent brute force attacks in the event that your database is breached and the hashes are leaked. The longer it takes for you to verify a password’s hash, the longer it’ll take an attacker to try to guess what the user’s password is based on the hash[^1].

> Use a hashing algorithm that is CPU intensive and not parallelizable on GPUs  

There’s a couple good options that meet these criteria; `Argon2`, `bcrypt`, and  `pbkdf2`. I would advise using Argon2Id as it’s hard to parallelize on GPUs, compute intensive, and easy to customize in most implementations. 

The second step is to set the right parameters for your chosen algorithm. To make the hashing easier or harder for an attacker, we can customize the behavior of the hashing algorithm by passing it different parameters.

> Hashing a password should take around 0.25 seconds, using as much CPU power and threads as you are able to provide.  

## Parameters
In the case of `bcrypt` and `pbkdf2`, these parameters are the number of rounds that we run for. For example, two rounds of `pbkdf2` is the equivalent of running `pbkdf2(pbkdf2(password))`, similar for bcrypt.

For Argon2, there’s more available options. We can set the number of cores, or the amount of CPU intensity it takes. 

Now, how do we find out which params to use? Essentially, you want to tweak the parameters until you find something that meets your requirements (0.25 seconds + compute intensive). I’ve written a command line tool that you can run on your production infrastructure to find out the best parameters for your particular environment, check it out at [`rzhade3/hash-metrics`](https://github.com/rzhade3/hash-metrics). 

In case you don’t want to run this script, and just want general guidance, consult the following table.

Up to date as of May 2022

| Algorithm     | Rounds |
|---------------|--------|
| pbkdf2_sha256 | 400000 |
| pbkdf2_sha512 | 300000 |
| bcrypt        | 12     |

However, your work isn’t done once you’ve selected the right params, as hardware keeps getting better every year! You need to ensure that your algorithm continues to remain secure, and that your parameters still provide adequate brute force protection on modern hardware. 

## Pointers

To future proof your implementation, I’d recommend a few follow up steps:

1. Use a hash algorithm implementation that takes as input a _time cost_ and _memory cost_, which will recalculate its own internal parameters to make sure that it’s always running with as many resources as possible. 
	* This will make things much easier on your part, as if you do this, you will not need to worry about Step 3. As long as your production infrastructure is upgraded reasonably frequently, your users will be protected according to industry standards. 
2. Store your hashing parameters in your database alongside your passwords
	* Given that you may change your hashing parameters in the future, it’s important to store what the current state of affairs is in your database. This will help you update them in the future.
	* For example, you can store the password like this:
		* `{algorithm=pbkdf}${param1=1},{param2=2}${hash}`
3. If you’re not using an implementation that uses a time cost or CPU cost, routinely reevaluate your parameters to check if they’re still intensive.
	* Moore’s law states that hardware doubles in power every 2 years. As such, you should re-evaluate your parameters on a 2 year cadence to ensure that it is still correct for your use case. 


[^1]: This is assuming that we’ve properly salted our creds, otherwise we’d still be vulnerable to Rainbow table lookups
