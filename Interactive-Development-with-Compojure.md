# Interactive Development with Compojure

One method for interactive development is to setup your routes in a way that it always references the `var` and not the value of the symbol. We can accomplish this by using the var-quote `#'` instead of the quote when passing our routes.

```clojure
=> (use 'ring.adapter.jetty)
=> (use 'compojure.core)
=> (require ['compojure.handler :as 'handler])
=> (defroutes main-routes (GET "/" [] (str "Hello Webserver")))
=> (def app (handler/site #'main-routes)) ; Notice the #' here.
=> (defonce (run-jetty (var app) {:port 8080 :join? false}))
```

Now there is a web-server running with the routes main-routes. As soon as you define a new route in your repl (or slime) you can see the effect in your browser.

To assist with developing you can also use the following helper functions.

```clojure
=> (defonce server (run-jetty (var app) {:port 8080 :join? false}))

=> (defn start-server []
     (.start server))

=> (defn stop-server []
     (.stop server))

=> (defn reload-server []
     (do (stop-server)
        (start-server)))
```