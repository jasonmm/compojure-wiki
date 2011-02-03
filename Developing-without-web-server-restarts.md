Restarting the web server after every tiny change is slow and tiresome. To speed up development you can set up automatic application reloading/redeployment.

# Using Ring middleware

One solution is to use [wrap-reload][wrap-reload] from the [ring-devel][ring] library. It reloads selected libraries before each request. Update your `project.clj` to include ring-devel as dependency:

```clojure
[ring/ring-devel "0.3.5"]
```

Next you need to wrap your route definition using wrap-reload. It takes a Ring handler function and a list of namespaces to be reloaded. Note that you need to pass the routes inside a var or the changes to them won't get loaded. Here's an adapted version of the [[Getting Started]] tutorial:

```clojure
(ns hello-www.core
  (:use compojure.core
        ring.middleware.reload)
  (:require [compojure.route :as route]))

(defroutes example
   (GET "/" [] "<h1>Hello World Wide Web!</h1>")
   (route/not-found "Page not found"))

;; #' is a reader macro for var. #'example is same as (var example).
(def wrapped-example
   (wrap-reload #'example '(hello-www.core)))
```

Run it from the REPL:

```clojure
=> (use 'ring.adapter.jetty)
=> (use 'hello-www.core)
=> (run-jetty wrapped-example {:port 8080})
```

Now test your app and make some changes to the source. They should be automatically take effect when you save the source.

[wrap-reload]: http://mmcgrana.github.com/ring/middleware.reload-api.html#ring.middleware.reload/wrap-reload
[ring]: https://github.com/mmcgrana/ring

# See also

* [StackOverflow: Compojure development without web server restarts](http://stackoverflow.com/questions/1665760/compojure-development-without-web-server-restarts)
* [Chas Emerick: Sane web development with Compojure, Jetty, and Maven](http://cemerick.com/2010/01/08/sane-web-development-with-compojure-jetty-and-maven/)