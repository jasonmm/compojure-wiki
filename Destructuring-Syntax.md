Compojure supports two sorts of destructuring. The Clojure kind, as one might use in the `let` [special form](http://clojure.org/special_forms), and a Compojure-specific kind designed for parsing parameters from a request map.

If you supply a map or symbol, Clojure's destructuring syntax will be used on the Ring request map. For example, here is how you'd bind a specific parameter to a variable using the Clojure syntax:

```clojure
(GET "/:foo" {{foo :foo} :params}
  (str "Foo = " foo))
```

But this is quite verbose, so Compojure has a more specialised form of destructuring. If you supply a vector, Compojure will use this custom destructuring syntax. The above example can be written more concisely as:

```clojure
(GET "/:foo" [foo]
  (str "Foo = " foo))
```