Compojure supports two sorts of destructuring for parameters passed in on the request:

 1. The [Clojure kind](https://clojure.org/reference/special_forms#binding-forms), as one might use in a `let` or with function arguments.
 2. A Compojure-specific kind designed for parsing parameters from the request map.

This page explains Compojure destructuring, but there is a [Quick Reference at the bottom](#quick-reference) as well.

## Prerequisite Knowledge

This documents assumes you:

1. have read the previous wiki pages.
1. understand Clojure destructuring.
1. are familiar with [Ring concepts](https://github.com/ring-clojure/ring/wiki/Concepts), especially the [request map](https://github.com/ring-clojure/ring/wiki/Concepts#requests).

Note, all the code snippets on this page belong inside a `(defroutes)` call.


## Regular Clojure Destructuring

Passing one symbol causes the entire request map to be bound to that variable.  For example, in this code snippet:

```clojure
(GET "/" request
  (str request))
```

the second argument to `GET` is a single variable name.  This variable will contain the entire request map and can be used inside the code that creates the response.

If you supply a map or symbol, Clojure's destructuring syntax will be used on the Ring request map. For example, here is how you'd bind a specific parameter to a variable using the Clojure syntax:

```clojure
(GET "/user/:id" {{userId :id} :params}
  (str "The user ID is " userId))
```


## Compojure-specific Destructuring

Since regular destructuring can be quite verbose, Compojure offers a more specialised form of destructuring. If you supply a vector, Compojure will use this custom destructuring syntax. The above example can be written more concisely as:

```clojure
(GET "/user/:id" [id]
  (str "The user ID is " id))
```

Notice that the variable name inside the vector (`[id]`) matches the name of the parameter placeholder (`:id`).

Query parameters are handled in the same way. Building on our example from an earlier wiki page we can modify our `/user/:id` route to accept a query string parameter as well.

```clojure
(GET "/user/:id" [id greeting]
  (str "<h1>" greeting " user " id "</h1>"))
```

Some cURL examples demonstrating usage of the above route:

```
$ curl http://localhost:3000/user/jack?greeting=hello
<h1>hello user jack</h1>

$ curl http://localhost:3000/user/jack?greeting=goodbye
<h1>goodbye user jack</h1>

$ curl http://localhost:3000/user/jack
<h1> user jack</h1>
```

Compojure's parameter destructuring syntax has three pieces of functionality. The first is to bind parameters directly to symbols of the same name. For instance, assume we have the following request map (we will continue to use this request map throughout the rest of this page):

```clojure
{:request-method :get
 :uri "/foobar"
 :headers {}
 :params {:x "foo", :y "bar", :z "baz", :w "qux"}}
```

Then we can use a vector of symbols to bind each parameter:

```clojure
(GET "/foobar" [x y z]
  (str x ", " y ", " z))
```

Requesting this route will create the response "foo, bar, baz".  Notice that the `w` parameter, while a part of the request map, is not bound to a variable.

To bind a map of all unassigned parameters, you can use the "&" symbol, followed by a variable name:

```clojure
(GET "/foobar" [x y & z]
  (str x ", " y ", " z))
```

Testing the above route with cURL:

```
$ curl "http://localhost:3000/foobar?x=foo&y=bar&z=baz&w=qux" 
foo, bar, {:z "baz", :w "qux"}
```

we can see that the variable `x` has been assigned the value of "foo", `y` has been assigned the value of "bar" and `z` is a map of the remaining parameters.

This behaviour is analogous to the "&" as it is used in normal Clojure bindings. The difference is that instead of getting the unbound remainder as a list, we get the unbound remainder as a map.

#### Request binding

If you want to access other parts of the request map, you can use the `:as` keyword to assign the entire request map to a symbol:

```clojure
(GET "/foobar" [x y :as r]
  (str x ", " y ", " r))
```

The above route will have the following variable assignments

```
x = "foo"
y = "bar"
r = {:request-method :get
      :uri "/foobar"
      :headers {}
      :params {:x "foo", :y "bar", :z "baz", :w "qux"}}
```

You can also bind a Clojure destructuring map to the `:as` keyword. This will allow specific keys in the request map to get bound to a named variable and the rest of the request map will not be bound (i.e. the rest of the request map will not be available to the code that creates the response).  Here's how to use the `:as` keyword to get only specific keys from the request map:

```clojure
(GET "/foobar" [x y :as {u :uri rm :request-method}]
       (str "'x' is \"" x "\"\n"
            "'y' is \"" y "\"\n"
            "The request URI was \"" u "\"\n"
            "The request method was \"" rm "\""))
```

Again, using cURL we can see how the variables get assigned:

```
$ curl "http://localhost:3000/foobar?x=foo&y=bar&z=baz&w=qux"
'x' is "foo"
'y' is "bar"
The request URI was "/foobar"
The request method was ":get"
```

#### Parameter coercion

As of Compojure 1.4.0, you can also supply coercion functions for parameters using the `:<<` keyword:

```clojure
[x :<< as-int]
```

In the above case, the parameter `x` will be passed through the `as-int` function before being assigned. If any coercion function returns `nil`, then the coercion is considered to have failed, and the route will not match.

The [compojure.coercions](https://weavejester.github.io/compojure/compojure.coercions.html) namespace contains two built-in coercions, `as-int` and `as-uuid`.

# Quick Reference

#### Regular Clojure Destructuring

* Get the `request` map directly:

```clojure
(GET "/" request
  (str request))
```

* Bind a specific parameter:

```clojure
(GET "/user/:id" {{userId :id} :params}
  (str "The user ID is " userId))
```

#### Compojure Specific Destructuring

```clojure
(GET "/user/:id" [id]
  (str "The user ID is " id))
```

* Binding multiple parameters:

```clojure
(GET "/user/:id" [id greeting]
  (str "<h1>" greeting " user " id "</h1>"))
```

```clojure
(GET "/foobar" [x y z]
  (str x ", " y ", " z))
```

* Mapping unassigned parameters:

```clojure
(GET "/foobar" [x y & z]
  (str x ", " y ", " z))
```

* Binding parameters and the entire request map:

```clojure
(GET "/foobar" [x y :as r]
  (str x ", " y ", " r))
```

* Binding specific keys from the request map:

```clojure
(GET "/foobar" [x y :as {u :uri rm :request-method}]
       (str "'x' is \"" x "\"\n"
            "'y' is \"" y "\"\n"
            "The request URI was \"" u "\"\n"
            "The request method was \"" rm "\""))
```
