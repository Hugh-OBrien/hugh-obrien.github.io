---
layout: page
title: About
permalink: /about/
---

I'm a [PhD candidate at Kings College London](http://www.imagingcdt.com/students/student-profiles/hugh-obrien) working imaging cardiac scar using CT via machine learning and other advanced processing techniques.

I also work part time at [Overleaf](https://www.overleaf.com/).

I have a BA in psychology from Trinity College Dublin and an MSc in computer science from Imperial College London. Between the two I worked at [PLOS](https://www.plos.org/) on a couple of their journals.

This site lagrely serves for my own use to motivate progress on personal projects and is updated infrequently.

If you find any of this useful or interesting please feel free to reach out, it's been cool to hear back from people about things I've put on here already. Email and some social media in the footer for anyone wanting to get in touch.

----------

For some mid 2000s nostalgia here's a cursor selector for the site:
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
