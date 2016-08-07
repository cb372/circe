---
layout: default
title:  "Encoding and decoding"
section: "codec"
---

# Encoding and decoding

circe uses `Encoder` and `Decoder` type classes for encoding and decoding. An `Encoder[A]` instance
provides a function that will convert any `A` to a `JSON`, and a `Decoder[A]` takes a `Json` value
to either an exception or an `A`. circe provides implicit instances of these type classes for many
types from the Scala standard library, including `Int`, `String`, and [others][encoder]. It also
provides instances for `List[A]`, `Option[A]`, and other generic types, but only if `A` has an
`Encoder` instance.

## Semi-automatic derivation

Sometimes you might need to have an `Encoder` or `Decoder` defined in your code. In this case
semi-automatic derivation can help, and it's easy to use. You'd write:

```scala
import io.circe._, io.circe.generic.semiauto._

case class Foo(a: Int, b: String, c: Boolean)

implicit val fooDecoder: Decoder[Foo] = deriveDecoder[Foo]
implicit val fooEncoder: Encoder[Foo] = deriveEncoder[Foo]
```

Or simply:

```scala
implicit val fooDecoder: Decoder[Foo] = deriveDecoder
implicit val fooEncoder: Encoder[Foo] = deriveEncoder
```

## @JsonCodec

circe-generic includes a `@JsonCodec` annotation that simplifies the use of semi-automatic generic derivation:

```scala
import io.circe.generic.JsonCodec, io.circe.syntax._
// import io.circe.generic.JsonCodec
// import io.circe.syntax._

@JsonCodec case class Bar(i: Int, s: String)
// defined class Bar
// defined object Bar

Bar(13, "Qux").asJson
// res2: io.circe.Json =
// {
//   "i" : 13,
//   "s" : "Qux"
// }
```

This works with both case classes and sealed trait hierarchies.

### forProductN helper methods

It's possible to construct encoders and decoders for case class-like types in a relatively boilerplate-free way
without generic derivation:

```scala
import io.circe._, io.circe.jawn._, io.circe.syntax._

case class User(id: Long, firstName: String, lastName: String)

object UserCodec {
  implicit val decodeUser: Decoder[User] =
    Decoder.forProduct3("id", "first_name", "last_name")(User.apply)

  implicit val encodeUser: Encoder[User] =
    Encoder.forProduct3("id", "first_name", "last_name")(u =>
      (u.id, u.firstName, u.lastName)
    )
}
```

It's not as clean or as maintainable as generic derivation, but it's less magical, it requires nothing
but circe-core, and if you need a custom name mapping it's currently the best solution
(until configurable generic derivation is released in 0.5.0).

{% include references.md %}
