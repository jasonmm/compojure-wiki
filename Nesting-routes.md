Routes can be nested using the `context` macro.

The simplest form might look like so:

```clojure
(defroutes main-routes
  (context "/api" []
    (GET "/something" [] ...)         ; matches /api/something
    (GET "/something-else" [] ...)))  ; matches /api/something-else
```

The inner routes might share common binding:

```clojure
(defroutes main-routes
  (context ["/v/:num", :num #"[0-1]"] [num]
    (GET "/entry" [] ...)         ; matches /v/0/entry and /v/1/entry
    (GET "/something" [] ...)))   ; matches /v/0/something and /v/1/something
```

`num` is also accessible inside the nested `GET` routes.

You might want to group nested routes into a `defroutes` when things get complex:

```clojure
(defroutes api-routes
  (GET "/something" [] ...)      ; matches /something
  (GET "/something-else" [] ...) ; matches /something-else

(defroutes main-routes
  (context "/api" [] api-routes) ; matches /api/something and /api/something-else
  ...)
```

When bindings is captured from the outer route, the inner `defroutes` has to take extra arguments to access those bindings.

```clojure
(defroutes some-routes [id]
  (GET "/something" [] (str id))
  (GET "/something-else" [] ...))

(defroutes main-routes
  (context ["/api/:id", :id #"[0-1]"] [id]
    ...))
```