Routes can be nested using the context macro:

```clojure
(defroutes api-routes
  (GET "/something" [] ...))   ; matches /something

(defroutes main-routes
  (context "/api" [] api-routes) ; matches /api/something
  other-routes)
```