---
layout: page
title: poetica
description: a personalized poetry experience
img: assets/img/poetica/poetica.png
importance: 1
category: technical
related_publications: false
---

Poetica started out as a term project for my Web Application Design course
at Carnegie Mellon University that I co-created with my friend Jaspreet. 
That semester, I had also gotten really into Sylvia Plath's poetry, and began
annotating poetry books that my friends had gifted me. I wanted to create
a website that made me feel the way I felt when I was curled up in front of
my blistering floor heater, sheltered from the Pittsburgh winter, annotating
The Bell Jar. 

The website displays personalized poetry recommendations for the user through
our 'discovery' page. Then, the user is taken to a set of poems that match
their discovery queries in the 'poem-view page,' which they can star to
save for later. Their saved poetry and a short bio will then go in their
'profile' page.


<div class="row">
    <div class="col-sm-6 mt-3">
        {% include figure.liquid loading="eager" path="assets/img/poetica/register_page.png" title="example image 1" class="img-fluid rounded z-depth-1" %}
        <div class="caption text-center"><em>Login/register page.</em></div>
    </div>
    <div class="col-sm-6 mt-3">
        {% include figure.liquid loading="eager" path="assets/img/poetica/discover_page.png" title="example image 2" class="img-fluid rounded z-depth-1" %}
        <div class="caption text-center"><em>Discover page.</em></div>
    </div>
</div>
<div class="row">
    <div class="col-sm-6 mt-3">
        {% include figure.liquid loading="eager" path="assets/img/poetica/profile_page.png" title="example image 3" class="img-fluid rounded z-depth-1" %}
        <div class="caption text-center"><em>Profile page.</em></div>
    </div>
    <div class="col-sm-6 mt-3">
        {% include figure.liquid loading="eager" path="assets/img/poetica/poemview.png" title="example image 4" class="img-fluid rounded z-depth-1" %}
        <div class="caption text-center"><em>Poem-view page.</em></div>
    </div>
</div>

We wanted readers to be able to choose their own experience with poetica. This
meant presenting the option of choosing a poet they wanted to hear from, choosing
keywords they wanted present in their poems, and emotions they wanted the readings
to evoke. We did this by parsing a Kaggle database of every work in The Poetry Foundation's database, and tagging data with emotion-keywords. We deployed the server with Django. 

As an avid Pinterest user, I also wanted users to be able to save their favorite
poems to a Pinterest board. So, I integrated the website with Pinterest API. Starring a poem now allows users to save the poetica poem-view page to their board, which also links to the poem on the Poetry Foundation's website. 

Jaspreet had an idea to integrate commenting functionality under each poem, which we did using Ajax and jQuery. 

This was my first time developing a web application from start to finish, from scratch, and I was truly shocked at how much goes into making a website visually appealing. However, the end product also shocked me: we had created something truly beautiful. Download poetica [here](https://github.com/aditinnara/poetica).