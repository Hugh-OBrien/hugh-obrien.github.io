---
layout: page
title: About
permalink: /about/
---

I'm a software developer currently working at [Overleaf](https://www.overleaf.com/) in London.

I have a balechors in psychology from Trinity College Dublin and a masters in computer science from Imperial College London. Between the two I worked at [PLOS](https://www.plos.org/) in publications.

This site lagrely serves for my own use to motivate progress on personal projects. It's also worked out as the logical place to keep progress and results/links.

Projects tend to relate to:

- Machine Learning: I've used Tensorflow and Caffe in a university setting and continue to do so in my spare time
- Web: I've worked with Django, Rails, Node and Jekyll (what this site is on)
- Game Design
- Algorithmically Generated Art... coming soon...

If anyone finds any of this useful or interesting please feel free to reach out, it's been cool to hear back from people about things I've put on here. Email and some social media in the footer.

Here's a cursor selector:
<div id="cursor-pick">
  <img id="cursor-img" src="">
</div>

<script
    src="https://code.jquery.com/jquery-3.2.1.min.js"
    integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
    crossorigin="anonymous">
</script>

<script>
 $("#cursor-pick").click(function(){
   curIndex++;
   if(curIndex === cursors.length){
     curIndex = 0;
   }
   updateCursor();
   setCookie("cursorIndex", curIndex, 5)
 });

 function populateSelector() {
   if(curIndex === 0 || curIndex == '0'){
     $('#cursor-img').attr("src","/assets/cursors/" + cursors[0]);
   }else{
     $('#cursor-img').attr("src","/assets/cursors/" + cursors[curIndex]);
   }
 }
 populateSelector();
</script>
