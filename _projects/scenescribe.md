---
layout: page
title: SceneScribe
description: a visual aid to improve lecture experiences
img: assets/img/scenescribe/cad_diag.png
importance: 1
category: technical
related_publications: false
---

SceneScribe is a system capable of improving lecture experiences for visually-impaired students by narrating text and graph data displayed on instructors’ presentation slides. My team members, Nithya and Jaspreet, and I created SceneScribe over the course of four months as our undergraduate capstone project. SceneScribe aims to reduce the information-disparity between visually-impaired and sighted students.

The current solution for visually-impaired students to read from lecture slides is to upload a photo of the slide and receive spoken output of extracted text, which is inconvenient and time-consuming. Our solution is a camera attachment to glasses which will capture an image of a slide, match it to a pre-uploaded lecture slide using a combined detection-classification approach, extract text and graph descriptions with PDFReader and a CNN-LSTM, and read them aloud to the user through an iOS app 10% faster than current solutions.

<div class="row d-flex">
    <div class="col-md-8 mt-3 d-flex flex-column">
        <div class="img-container d-flex align-items-center justify-content-center" style="height: 100%">
            {% include figure.liquid loading="eager" path="assets/img/scenescribe/physical_diag.png" title="Larger image" class="img-fluid rounded z-depth-1 w-100" style="object-fit: cover; height: 100%" %}
        </div>
        <div class="caption text-center"><em>Our device, inside and outside of the component case.</em></div>
    </div>
    <div class="col-md-4 mt-3 d-flex flex-column">
        <div class="img-container d-flex align-items-center justify-content-center" style="height: 100%">
            {% include figure.liquid loading="eager" path="assets/img/scenescribe/cad_diag.png" title="Smaller image" class="img-fluid rounded z-depth-1 w-100" style="object-fit: cover; height: 100%" %}
        </div>
        <div class="caption text-center"><em>A CAD model of our device.</em></div>
    </div>
</div>

We developed an iOS app that will store a list of classes that the user is taking. Before going to their lecture, the user will indicate which class they are going to by clicking a button corresponding to their courseID. The app is set up with haptic feedback and Apple's VoiceOver to provide easy access to visually-impaired users.

<div class="row justify-content-center">
    <div class="col-sm-5 mt-3 mt-md-0 text-center">
        <div class="img-container">
            {% include figure.liquid loading="eager" path="assets/img/scenescribe/ios_app.png" title="example image" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption mt-2">
            <em>iOS app for course configuration.</em>
        </div>
    </div>
</div>

After this selection, the most recent lecture PDF from the corresponding course will be scraped and uploaded to our server. Although this is a minor inconvenience for the user, we decided to prioritize accuracy by reading directly from the slides rather than a lower quality image.

The server will be hosted on a Jetson, which will be on campus at a central location (such as the disability resources office), and our app will communicate with it wirelessly.

Then, each slide of the PDF will be analyzed for the existence of text and graphs; in particular, we will run a modified YOLO-v5 model to retrieve bounding boxes around the particular types of graphs we are considering (line graphs and scatterplots). The text will be extracted with a PDF reader (PyMuPDF), and the graph descriptions will be extracted using our trained CNN-LSTM model. Then, all of the associated text for each slide will be stored in a JSON so that it can be easily accessed during lecture.

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0 text-center">
        <div class="img-container">
            {% include figure.liquid loading="eager" path="assets/img/scenescribe/pipeline.png" title="example image" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption mt-2">
            <em>Graph and text extraction model.</em>
        </div>
    </div>
</div>

During the lecture, when the user wants to hear a slide description during lecture, the user will press the start button on the side of the glasses attachment, which will indicate to the Raspberry Pi that it should capture an image. The image will then be wirelessly sent over WiFi to a server hosted on the Jetson.

Once received, the slide number (contained in a red box in the bottom right corner of the image) will be detected, cropped, and preprocessed before being passed to the classification algorithm, which detects image contours. Then, we run a digit-classification model (a standard CNN) to classify the digit. Finally, these digits are then strung together to get the final slide number.

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0 text-center">
        <div class="img-container">
            {% include figure.liquid loading="eager" path="assets/img/scenescribe/slide_classify.png" title="example image" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption mt-2">
            <em>Slide number extraction pipeline.</em>
        </div>
    </div>
</div>

Once the slide number is identified, we can fetch the stored text and graph description for that slide (which was already extracted and stored in a JSON) and send it to our iOS app, where it is read aloud to the user using text-to-speech. While this text description is being played, the user can press the stop button at any time when they don’t want to hear the audio anymore.

We computed a character error rate (CER) of 0% on 50 well-formatted lecture slides, a 98.2% accuracy on slide number extraction with similarly-formatted slides, and a 95% accuracy on graph detection using Intersection over Union (IoU). We performed user testing with visually-impaired and sighted students and received average scores of 4.9 for text accuracy, 3.9 for graph description accuracy, and 4.0 for ease of use on a scale of 0 to 5.

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0 text-center">
        <div class="img-container">
            {% include figure.liquid loading="eager" path="assets/img/scenescribe/test_res.png" title="example image" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption mt-2">
            <em>Testing results.</em>
        </div>
    </div>
</div>

I had always been interested in wearable accessible technology, and I am highly passionate in lowering the information gap among historically underprivileged groups. This project is important to me, and I'm interested in exploring more avenues in accessible tech.

You can download our final report [here](https://drive.google.com/file/d/1kipu5k4wk-9ibMJPXmkZDjckchq28zz0/view?usp=sharing), or visit our <a href="http://course.ece.cmu.edu/~ece500/projects/f23-teamb3/">project website</a> for more info.
