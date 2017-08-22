---
layout: post
title:  "Compiling Tensorflow for Unity3d"
date:   2017-02-12
categories: blog
---

I've been thinking for a while about how best to combine machine learning knowledge I've built up and my other hobby - making video games. To this end I've been looking into using Tensorflow with Unity3d. I forsee a lot of issues around performance at runtime, along with cross platform issues issues down the road.


![Tensor-and-unity]({{ site.url }}/assets/images/tensor-unity.png)

<!--more-->
As a start here is a quick rundown of compiling Tensorflow to run a trained graph from a C# Unity script using the C++ api.

---

### 1. Getting a Graph to Use
The idea here is to keep it to a bare minimum for what you need. The goal here is to make sure we can get TF will run at all rather than spending time making it do something useful. For these first couple of steps I borrowed heavily from [Jim Flemming's excellent Medium post](https://medium.com/jim-fleming/loading-a-tensorflow-graph-with-the-c-api-4caaff88463f#) on the basics of using the C++ api, which we'll need. Using a simple python script you can generate a protobuf file to store your graph, I used:

```
import tensorflow as tf
import numpy as np

with tf.Session() as sess:
a = tf.Variable(5.0, name='a')
b = tf.Variable(6.0, name='b')
c = tf.maximum(a, b, name="c")

sess.run(tf.initialize_all_variables())

print a.eval()
print b.eval()
print c.eval()

tf.train.write_graph(sess.graph_def, 'models/', 'graph.pb', as_text=False)
```

which does the very impressive task of finding the max of two numbers and storing it in a variable `c`.

### 2. Compiling Tensorflow to a shared libaray (.so)
We can use the C++ api for TF to read and excetue graphs we've already saved pretty easily. In addition the Google documentation for using their build tool, [Bazel](https://bazel.build/) is pretty good so compiling the shared library isn't really too much of an issue. Here I used Jim Flemming's build script with a few changes.

### Debugging
When we start using this with Unity it's not the same as running a script internally, we have to recompile to add debug messages. There's not a simple method of accessing the Unity editor console to print useful debug messages anyway since we're constrained by the return type of our function. This is a problem even with this very simple example as we've hard coded the path to the protobuf file which will cause problems down the road. Unity isn't handling the paths so it won't put the files in sensible places when building for instance. To give us some useful output on errors I've added logging to a file instead of standard out and known return values so it's obvious where in the code we hit an error.

### Shared library
The function is incased in an `extern "C" {}`. Since we're not building an exceutable we can't just have a main function that returns 0, we want to return our max number. The C++ compiler however doesn't preserve function names so calling our function after compilation won't work, we have to tell the compiler we want to declare our funtion with C linkage. The [Unity documentation](https://docs.unity3d.com/Manual/PluginsForDesktop.html) does a fine job of explaining why we need to do this.

Here's the code put inside the Tensorflow repo at `/tensorflow/loader/loader.cc`

```
#include "tensorflow/core/public/session.h"
#include "tensorflow/core/platform/env.h"
#include <fstream>

using namespace tensorflow;

extern "C" {
  const int run() {
    // Initialize a tensorflow session
    Session* session;
    Status status = NewSession(SessionOptions(), &session);
    if (!status.ok()) {
      return 2;
    }

    std::ofstream logFile;
    logFile.open ("loader_output.txt");

    // Read in the protobuf graph we exported
    // (The path seems to be relative to the cwd. Keep this in mind
    // when using `bazel run` since the cwd isn't where you call
    // `bazel run` but from inside a temp folder.)
    // -----------------------------------------------------------------------------------
    // for Unity these paths are also important, the location of the graph.pb file matters
    // and will change depending on whether you're running from the editor or a build
    GraphDef graph_def;
    status = ReadBinaryProto(Env::Default(), "models/graph.pb", &graph_def);
    if (!status.ok()) {
      logFile << status.ToString() << "\n";
      return 4;
    }

    // Add the graph to the session
    status = session->Create(graph_def);
    if (!status.ok()) {
       logFile << status.ToString() << "\n";
       return 5;
    }

    // Setup inputs and outputs:

    // Our graph doesn't require any inputs, since it specifies default values,
    // but we'll change an input to demonstrate.
    Tensor a(DT_FLOAT, TensorShape());
    a.scalar<float>()() = 3.0;

    Tensor b(DT_FLOAT, TensorShape());
    b.scalar<float>()() = 2.0;

    std::vector<std::pair<string, tensorflow::Tensor>> inputs = {
      { "a", a },
      { "b", b },
    };

    // The session will initialize the outputs
    std::vector<tensorflow::Tensor> outputs;

    // Run the session, evaluating our "c" operation from the graph
    status = session->Run(inputs, {"c"}, {}, &outputs);
    if (!status.ok()) {
      logFile << status.ToString() << "\n";
        return 6;
      }

    // Grab the first output (we only evaluated one graph node: "c")
    // and convert the node to a scalar representation.
    auto output_c = outputs[0].scalar<float>();

    // Free any resources used by the session
    session->Close();

    logFile << status.ToString() << "\n";
    logFile.close();
    // return the output number as an integer
    return (int)output_c();
  }
}
```
We've hard coded here that it should output 3, which is the max of 3.0 and 2.0 cast to an `int`

We can compile using Bazel to either an exceutable or a shared library. The former is good to make sure the above code works, you can just throw the function into a main function and compile it. Once that's working we can make our shared library. The Bazel BUILD file for that looks like:

```
cc_binary(
    name = "loader.so",
    linkshared = 1,
    srcs = ["loader.cc"],
    deps = [
        "//tensorflow/core:tensorflow",
    ]
)
```
If you keep this inside the `loader` folder with the cc file we can build from that folder using `bazel build :loader`. The actual so we need will end up


### 3: Running in the Unity Editor
You can access the shared library using the `[DllImport ...]` statement for C#, it doesn't matter this isn't a dll! This works the same as any Unity plugin, I have it in `assets/plugins` Here's my very simple unity script:
```
public class PluginImport : MonoBehaviour {
	[DllImport ("loader")]
	private static extern int run ();

	//Lets make our calls from the Plugin

	void Start () {
		int output = run ();
		Debug.Log (output);
		gameObject.GetComponent<Text> ().text = output.ToString();
	}
}
```
All this is doing is:
- Loading our library
- On scene start running the `run` function from the library
- Putting the resulting integer as the string on an attached UI element.

Because of the way we hardcoded the model loading in the C++ and how the Unity editor does paths the protobuf needs to be in the root of the Unity project itself, not the plugins folder, not `/assets`. Now we can run this from the editor...

![Running in Unity Editor]({{ site.url }}/assets/images/unity-3.png)


... Very impressive Unity, 3 is the right answer...

### 4: Running in a build
The shared libaray we've made as far as I know will only work for Linux. We need to compile different plugins for other platforms. Within the Unity editor we can set plugins to be included in different builds from the inspector for the asset. By default everything gets included which is fine if we're only bulding for Linux but our project size will get pretty out of control if we have 3 or 4 TF libraries always being included in each build.

The default settings will work for the build; however this is where our lazy hard coding of paths is a problem. In this case the root path is going to be whereever your excutable is run from, so the `/models` folder needs to go there, not anywhere within the `/data` folder!

It's not particularly impressive but it works - we can access Tensorflow from a built Unity3d project! Now to run some more exciting graphs...
<br/>
<br/>
<br/>