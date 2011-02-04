Restarting the web server after every tiny change is slow and tiresome. To speed up development you can set up automatic application reloading/redeployment.

# Lein-Ring

[Lein-Ring][lein-ring] is a Leiningen plugin that can be used to start a development web server that automatically reloads modified source files. Add it as a dependency to your `project.clj`:

[lein-ring]: https://github.com/weavejester/lein-ring/

```clojure
:dev-dependencies [[lein-ring "0.3.0"]]
```

You also need to add a new `ring` key, which specifies your Ring handler function (that is, your routes definition). For example, here's an updated `project.clj` for the [[Getting started]] tutorial:

```clojure
(defproject hello-www "1.0.0-SNAPSHOT"
  :description "A Compojure 'Hello World' application"
  :dependencies [[org.clojure/clojure "1.2.0"]
                 [org.clojure/clojure-contrib "1.2.0"]
                 [compojure "0.5.3"]
                 [ring/ring-jetty-adapter "0.3.1"]]
  :dev-dependencies [[lein-ring "0.3.0"]]
  :ring {:handler hello-www.core/example})
```

Now run the command `lein ring server` to start the web server.

# Using Ring middleware

Another solution is to use [wrap-reload][wrap-reload] from the [ring-devel][ring] library. It reloads selected libraries before each request. Update your `project.clj` to include ring-devel as a dependency:

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
* [Compojure Google Group: Reload issues](http://groups.google.com/group/compojure/browse_thread/thread/5e057d04e39fd717) (with instructions for [lein-ring](https://github.com/weavejester/lein-ring))