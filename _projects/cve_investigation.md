---
layout: page
title: iTop CVE Investigation
description: replicating, extending, and combining CVEs found on an IT portal
img: assets/img/scenescribe/cad_diag.png
importance: 1
category: technical
related_publications: false
---

The CVE program aims to catalogue 'Common Vulnerabilities and Exposures' of hardware and software systems. One of our term projects for Offensive Security was to find a CVE without a proof of concept, and with a high CVSS (Common Vulnerability Scoring System) score, and develop an exploit that takes advantage of the vulnerability. When my group and I were first looking for CVEs, we noticed a substantial group of CVEs for the same platform: iTop.

<!-- pics of itop dash -->

iTop is an open-source software for IT service management, configuration management databases (CMDBs), and other IT processes. IT admins can log on to iTop's dashboard to manage incidents, users, and configurations. Interestingly, among iTop's clients is the Council of Europe. We decided that, because there were seemingly so many security vulnerabilities for iTop, that this would be a good route for our project. The CVEs were CVE-2024-52002, CVE-2024-52000, CVE-2024-31998, CVE-2024-31448. All of these were reported around November 4 to November 8 of 2024, so it didn't seem like a stretch to assume that they were related. The CVE details are as follows, and all apply to versions below iTop v3.2.0.

- **_CVE-2024-52002_**: Several URL endpoints are subject to a CSRF vulnerability, including the creation and modification of users, people, and the viewing of sensitive information like passwords.
- **_CVE-2024-52000_**: Several URL endpoints are subject to an XSS vulnerability.
- **_CVE-2024-31998_**: A CSRF can be performed on CSV import simulation.
- **_CVE-2024-31448_**: An XSS Attack can be performed by injecting malicious JavaScript into CSV content before it is simulated.

<!-- pictures of each -->

I decided to focus on the latter two CVEs because they seemed related, and both involved CSV imports. In order to import a CSV (which may contain new company data, new configs, and importantly, new iTop users), we must go through the iTop CSV Import Wizard. The following are the steps we must take in order to fully import a CSV:

1. _Upload a CSV_ by copy-and-pasting it or loading from a file.
2. _Choose CSV data options_, like modifying the default separator characters if your CSV is oddly-formatted.
3. _Data mapping_, which allows you to choose the type of data that you are importing (whether it's a new user, model, location, etc.).
4. _Import simulation_, which shows you a formatted table of what the data you are importing will look like in the database.
5. _Import completion_, which actually runs the import and loads new objects into the iTop database.

<!-- pictures -->

My first assumption was that CVE-2024-31998 would allow us to import any CSV data we wanted, because there was insufficient validation of CSRF tokens (or there were no CSRF tokens present). So, I tried making the following POST request to the _Import completion_ page, which contains a CSV with a new user:

<!-- pic -->

However, iTop presented me with the following error: "CSRF token invalid" and refused me access to the fifth step. I looked at the form data in the previous page, and lo and behold, iTop had included an anti-CSRF token embedded within the hidden fields of the form data: transaction_id. This transaction_id token is randomly generated upon every page load between the fourth and fifth steps. It appeared that the CVE was concerned only with the fact that there was no transaction_id to get to the fourth step, _Import simulation_, although this wasn't actually adding any user data -- it was just allowing the IT admin to view the data they would later upload in the fifth step. I tried multiple things to get around the transaction_id, none of which worked:

- Reusing the same transaction_id for multiple POST requests gave me a "CSRF reused" error.
- Not including a transaction_id in the POST request at all gave me a "CSRF invalid" error.
- Attempting to get the HTTP cookie itself didn't work because cookies were protected with the "HttpOnly" header, which doesn't allow this kind of extraction.

<!-- pic of error -->

Stuck, and with only a few days before the project was due, I decided to look into the XSS vulnerability. I tried injecting the following malicious JavaScript into the CSV content: JS. Luckily, the following alert popped up once I loaded the _Import simulation_, meaning remote code execution by way of JS injection into the CSV worked. However, this was certainly not enough to present on. I wondered then, was there any way to use this XSS vulnerability to retrieve the transaction_id token? I tried the following malicious JavaScript as a last-ditch effort before I abandoned the CSRF vulnerability for good: JS. To my excitement, I received the following alert.

<!-- pic of alert with admin-fhiudhso -->

Then, I immediately sent this admin token as a POST request to the fifth step, and I got through. This meant that, in order to create a full exploit, I would need to send two POST requests: one to retrieve the transaction_id token, and another to send the transaction_id with the new CSV I wanted to import. I also needed a victim to somehow submit these requests on the attacker's behalf. Here's what I came up with:

<!-- two side by side code -->

The first page, first_step.html, injects JavaScript into a CSV to make a POST request to the import simulation page, and retrieves the transaction_id token. This transaction_id is passed along as a URL parameter to the endpoint where last_step.html is hosted. Then, last_step.html extracts the transaction_id token from the query string. The transaction_id is sent to the _Import completion_ step along with a CSV defining a new admin user we want to upload to iTop. Now, we are able to login as admin to the iTop page without having any credentials or knowledge of the victim's credentials.

<!-- full demo video -->

Here are some nuances of the exploit:

- We use '&\quot;' instead of quotation marks in our malicious JavaScript here: JS, otherwise iTop will not parse the CSV properly.
- We host these HTML pages on a server and rely on the victim clicking a link that takes them to the first_step.html endpoint, because CSRF relies on the victim being authenticated via their session token.
- The malicious code is executed immediately after the victim loads our webpage because I've set everything to occur 'onLoad'

This full exploit is an extension of existing CVEs - neither of the CVEs I focused on said anything about getting through to the actual upload of malicious CSV content, just that the CSV can be _simulated_.

I then turned my attention back to the first two CVEs that my other team members were working on. Unfortunately, they found that nothing interesting could be done there because the forms that could actually cause damage (like adding new admin users) were protected by the same transaction_id CSRF token. In these cases, there was no possibility for XSS attacks to retrieve the token, so there was no way to get past it.

<!-- We looked into how iTop patched these issues in v3.2.0+, and it was quite simple. They added the following change:

pictures

This addition of a transaction_id token requires that they

The full exploit can be found here. -->

Finally, I decided to test the exploit on the latest version of iTop. Unsurprisingly, I found that the CSRF had been patched. However, I found that the XSS attack actually had not been patched, and I ended up emailing the iTop team. They confirmed that my exploit still worked, and awarded me my first CVE!
