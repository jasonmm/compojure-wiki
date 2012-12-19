Routes can be nested using the context macro:

```clojure
(defroutes main-routes
  (context "/api" [] api-routes)
  other-routes)

  (defroutes api-routes
    (GET "/something" [] ...)   ; matches /api/something
```
