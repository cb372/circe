---
layout: default
title:  "Optics"
section: "optics"
---

# Optics

Optics are a powerful tool for traversing and modifying JSON documents. They can reduce boilerplate 
considerably, especially if you are working with deeply nested JSON.

circe provides support for optics by integrating with [Monocle](monocle). To use them, add a
dependency on `circe-optics` to your build:

```scala
libraryDependencies += "io.circe" %% "circe-optics" % circeVersion
```

TODO examples

{% include references.md %}
