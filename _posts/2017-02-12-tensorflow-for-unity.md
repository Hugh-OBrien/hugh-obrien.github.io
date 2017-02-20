---
layout: post
title:  "Compiling Tensorflow for Unity3d"
date:   2017-02-12
categories: blog
---

I've been thinking for a while about how best to combine machine learning knowledge I've built up and my other hobby - machine learning. To this end I've been looking into using Tensorflow with Unity3d. I forsee a lot of issues around how this would perform at runtime, along with cross platform issues issues down the road.
As a start here is a quick rundown of how you can compile Tensorflow to run a graph from a Unity script.

<!--more-->

![Tensor-and-unity]({{ site.url }}/assets/images/tensor-unity.png)

- 

Running pre-trained Tensorflow graphs from inside C# scripts
My first attempt involved compiling the image recognition example from the Tensorflow repo as a shared library. We can access code at runtime using shared libraries. Google document how export Tensorflow api code to a .so with Bazel, which as far as I can tell limits me to using the Linux build of Unity as I couldn’t see it was possible to build dlls for Windows. Unity has proved to be mildly unstable on the Linux partition on my machine in the past but we’ll deal with that later down the road. It’s also unclear early on that we’ll be able to build any finished Unity projects to Windows without the correct version of the shared library, but again we’ll deal with that once it’s up and running in some form.
Some small changes are required to make the existing code into a shared library. The BUILD file needs to be told it’s building a shared library with
linkshared = 1
Additionally the main function of the .cc file needs to be enclosed in
extern "C"{}
Otherwise the compiler is going to mess with the name of the function making it difficult to access in the Unity scripts.
Now how did making these minuscule changes go? Compiling to the shared library worked fine as expected. I also know that Unity accesses the shared library because it throws an error when I try call incorrect functions from the file. However when we try run the main function the engine simply crashes… Likely there’s something going wrong with the shared library!
Complexity
Lets try something simpler. For some classic machine learning lets try use a trained MNIST network from the Tensorflow examples.