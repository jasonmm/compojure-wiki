Routes can be nested using the `context` macro:

```clojure
(defroutes api-routes
  (GET "/something" [] ...)      ; matches /something
  (GET "/something-else" [] ...) ; matches /something-else

(defroutes main-routes
  (context "/api" [] api-routes) ; matches /api/something and /api/something-else
  other-routes)
```