Compojure supports two sorts of destructuring:

 1. The Clojure kind, as one might use in the `let` [special form](http://clojure.org/special_forms), and
 2. a Compojure-specific kind designed for parsing from the request map:
      * query string (and form) parameters, and
      * portions of the url path;

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

## REPL Demonstration

Route call responses are shown after comment tags ;;-> 

```clojure
user> (use 'compojure.core)
nil
user> (require '[compojure.handler :as handler])
nil
user> (require '[compojure.route :as route])
nil

user> (def my-request
        {:request-method :get
         :uri "/my-uri"
         :headers []
         :params {:x "foo" :y "bar" :z "baz" :w "qux"}})
#'user/my-request

user> (defroutes my-3-parameter-route
        (GET "/:my" [x y z]
          (str "x -> " x "; "
               "y -> " y "; "
               "z -> " z)))
#'user/my-3-parameter-route

user> (my-3-parameter-route my-request)

;;-> {:status 200
;;->  :headers {"Content-Type" "text/html; charset=utf-8"},
;;->  :body "x -> foo; y -> bar; z -> baz"}

user> (defroutes my-2-parameter-and-remainder-route
        (GET "/:my" [x y & z] ; & binds remainder of request map to z
          (str "x -> " x "; "
               "y -> " y "; "
               "z -> " z)))
#'user/my-2-parameter-and-remainder-route

user> (my-2-parameter-and-remainder-route my-request)

;;-> {:status 200,
;;->  :headers {"Content-Type" "text/html; charset=utf-8"},
;;->  :body "x -> foo; y -> bar; z -> {:my \\"my-uri\\", :z \\"baz\\", :w \\"qux\\"}"} 

user> (defroutes my-remainder-symbol-route
        (GET "/:my" [x y :as r] ; :as keyword assigns entire request map to symbol
          (str "x -> " x "; "
               "y -> " y "; "
               "r -> " r)))
#'user/my-remainder-symbol-route

user> (my-remainder-symbol-route my-request)

;;-> {:status 200,
;;->:headers {"Content-Type" "text/html; charset=utf-8"},
;;->:body "x -> foo; y -> bar;
;;->       r -> {:route-params {:my \\"my-uri\\"},
;;->                            :request-method :get,
;;->                            :uri \\"/my-uri\\",
;;->                            :headers [],
;;->                            :params {:my \\"my-uri\\",
;;->                                     :z \\"baz\\",
;;->                                     :y \\"bar\\",
;;->                                     :x \\"foo\\",
;;->                                     :w \\"qux\\"}}"}

user> (defroutes my-destructuring-map-route
        (GET "/:my" [x y :as {u :uri}] ; :as keyword and destructuring map
                                       ; to bind :uri value to u
          (str "x -> " x "; "
               "y -> " y "; "
               "u -> " u)))
#'user/my-destructuring-map-route

user> (my-destructuring-map-route my-request)

;;-> {:status 200,
;;->  :headers {"Content-Type" "text/html; charset=utf-8"},
;;->  :body "x -> foo; y -> bar; u -> /my-uri"}
```