---
layout: post
title: State machines
---

The UI programming meme _Slaying a UI Antipattern in X_ refers to the 
`RemoteData` pattern for modeling data generated by a REST request. 
<sup><a href="#1">1</a>,</sup>
<sup><a href="#2">2</a>,</sup>
<sup><a href="#3">3</a>,</sup>
<sup><a href="#4">4</a>,</sup>
<sup><a href="#5">5</a></sup>
User interfaces generally present this kind of data, "remote data", in one of 
four states: not loaded, loading, error, and success.
It's common for UI programmers to derive these states from a model with this
shape: 

```elm
type alias Model = 
  { xs : List x
  , isLoading : Bool
  , error : String
  }
```

`RemoteData` improves on this approach by encoding the state of the data 
into its type:

```elm
type alias Model = 
    { xs : RemoteData
    }

type RemoteData 
    = NotAsked
    | Loading
    | Failure e
    | Success d
```

Making the state explicit has two benefits. 
First, the state is universally consistent and clear.
There is no longer a risk that a developer will incorrectly derive the implicit
state.
Second, consumers of the data are forced to handle every state.
There is less risk that buggy or unpredictable behavior is introduced by 
forgetting to handle a case.


```
+-------------------------------+
| Empty Pending                 |<-+-----+-----+
+-------------------------------+  |     |     |
                                   |     |     |
Pending----------------------------+     |     |
Success------------------------------+   |     |
Error----------------------------------+ |     |
                                     | | |     |
                                     | | |     |
+-------------------------------+    | | |     |
| Empty Valid                   |    | | |     |
+-------------------------------+    | | |     |
                                     | | |     |
Pending----------------------------------+     |
Success------------------------------------+   |
Error----------------------------------------+ |
                                     | |   | | |
                                     | |   | | | 
+-------------------------------+    | |   | | |
| Empty (Invalid e)             |<-----+-----+-----+
+-------------------------------+    |     |   |   |
                                     |     |   |   |
Pending----------------------------------------+   |
Success------------------------------------------+ |
Error----------------------------------------------+
                                     |     |     |
                                     |     |     |
+-------------------------------+    |     |     |
| Primed Pending Collection     |<---------------|---+-----+-----+
+-------------------------------+    |     |     |   |     |     |
                                     |     |     |   |     |     |
Pending----------------------------------------------+     |     |
Success------------------------------------------------+   |     |
Error----------------------------------------------------+ |     |
                                     |     |     |     | | |     |
                                     |     |     |     | | |     |
+-------------------------------+    |     |     |     | | |     | 
| Primed (Valid d) Collection   |<---+-----+-----+-----+-|-|-+---|-+
+-------------------------------+                        | | |   | |
                                                         | | |   | |
Pending----------------------------------------------------+ |   | |
Success------------------------------------------------------+   | |
Error----------------------------------------------------------+ | |
                                                         |     | | |
                                                         |     | | |
+-------------------------------+                        |     | | |
| Primed (Invalid e) Collection |<-----------------------+-----+-|-|-+
+-------------------------------+                                | | |
                                                                 | | |
Pending----------------------------------------------------------+ | |
Success------------------------------------------------------------+ |
Error----------------------------------------------------------------+
```


**Footnotes**

<ol>
  <li>
    <fn id="1">
      <a href="http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html">
        How Elm Slays a UI Antipattern
      </a>
      by Kris Jenkins, the first to describe this pattern.
    </fn>
  </li>
  <li>
    <fn id="2">
      <a href="https://medium.com/javascript-inside/slaying-a-ui-antipattern-in-fantasyland-907cbc322d2a">
        Slaying a UI Antipattern in Fantasyland
      </a>
    </fn>
  </li>
  <li>
    <fn id="3">
      <a href="https://medium.com/javascript-inside/slaying-a-ui-antipattern-in-react-64a3b98242c">
        Slaying a UI Antipattern in React
      </a>
    </fn>
  </li>
  <li>
    <fn id="4">
      <a href="https://medium.com/@gcanti/slaying-a-ui-antipattern-with-flow-5eed0cfb627b">
        Slaying a UI Antipattern in Flow
      </a>
    </fn>
  </li>
  <li>
    <fn id="5">
      <a href="https://gist.github.com/busypeoples/b8982f215642e5258d3d49a9aa7d7438">
        Slaying a UI Antipattern in ReasonML
      </a>
    </fn>
  </li>
</ol>