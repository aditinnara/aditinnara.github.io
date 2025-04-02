---
layout: page
title: CTF Development
description: creating capture the flag problems for picoCTF
img: assets/img/ctf/pico.png
importance: 1
category: technical
related_publications: false
---

Through my offensive security course, I was given the opportunity to develop two capture-the-flag (CTF) problems to be deployed in a nationwide CTF competition called picoCTF 2025. I quickly learned that developing CTFs was not at all like solving CTFs; it came with its own challenges. I struggled to create problems that struck the perfect balance between too easy and absolutely impossible. However, I ended up creating a two-part CTF that did the job well. 

Warning: Don't read the rest of this if you don't want the solutions to be spoiled! Feel free to play the CTF [here](https://play.picoctf.org) first.

# Guess The Cheese

Problem 1 deals with an affine cipher. I wanted to look into a different cipher/encryption scheme that we hadn't gone over in class. The solution approach is to realize that it is an affine cipher, then use the encryption oracle to encrypt a known plaintext. Then iterate over the possible a and b values to find out what values of a and b were used to encrypt it, then use these values to decrypt the encrypted cheese.

Problem 2 deals with an insufficiently salted hash. I've done a fair bit of research on issues that can arise from improper salts, and wanted to implement a simple version of this vulnerability. The solution involves creating a rainbow table with all possible 1 byte salts, then SHA-256 encrypting every cheese in cheese_list.txt, and cross referencing these encryptions with the encryption provided by the program in order to realize what the actual value is!

The challenge files are available [here](https://github.com/aditinnara/guess_my_cheese_CTF).

# PicoCTF 

After some testing of these problems, they are now live on the picoCTF website! You can play them [here](https://play.picoctf.org). Make sure to create an account and search "Guess My Cheese" to find my problems!

Thank you to my classmates for awarding me 'Best Problem' with a 100% upvote rate!