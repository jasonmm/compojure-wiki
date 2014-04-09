Compojure supports two sorts of destructuring:

 1. The Clojure kind, as one might use in the `let` [special form](http://clojure.org/special_forms), and
 2. A Compojure-specific kind designed for parsing parameters from the request map.

## Regular Clojure Destructuring

If you supply a map or symbol, Clojure's destructuring syntax will be used on the Ring request map. For example, here is how you'd bind a specific parameter to a variable using the Clojure syntax:

```clojure
(GET "/:foo" {{foo :foo} :params}
  (str "Foo = " foo))
```

## Compojure-specific Destructuring

Since regular destructuring can be quite verbose, Compojure offers a more specialised form of destructuring. If you supply a vector, Compojure will use this custom destructuring syntax. The above example can be written more concisely as:

```clojure
(GET "/:foo" [foo]
  (str "Foo = " foo))
```

Compojure's parameter destructuring syntax has three pieces of functionality. The first is to bind parameters directly to symbols of the same name. For instance, assume we have the following request map:

```clojure
{:request-method :get
 :uri "/foobar"
 :headers {}
 :params {:x "foo", :y "bar", :z "baz", :w "qux"}}
```

Then we can use a vector of symbols to bind each parameter:

```clojure
[x y z]
x -> "foo"
y -> "bar"
z -> "baz"
```

To bind a map of all unassigned parameters, you can use the "&" symbol, followed by a variable name:

```clojure
[x y & z]
x -> "foo"
y -> "bar"
z -> {:z "baz", :w "qux"}
```

This behaviour is analogous to the "&" as it is used in normal Clojure bindings. The difference is that instead of getting the unbound remainder of a list, we get the unbound remainder of a map.

Finally, you can use the `:as` keyword to assign the entire request map to a symbol:

```clojure
[x y :as r]
x -> "foo"
y -> "bar"
r -> {:request-method :get
      :uri "/foobar"
      :headers {}
      :params {:x "foo", :y "bar", :z "baz", :w "qux"}}
```

You can also bind a Clojure destructuring map to the `:as` keyword:

```clojure
[x y :as {u :uri}]
x -> "foo"
y -> "bar"
u -> "/foobar"}
```