Routes can be nested using the context macro:

```clojure
(defroutes api-routes
  (GET "/something" [] ...))   ; matches /api/something

(defroutes main-routes
  (context "/api" [] api-routes)
  other-routes)
```