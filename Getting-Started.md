The simplest way to get started with Compojure is to use [[Leiningen|http://github.com/technomancy/leiningen]]. Installing Leiningen is straightforward, and instructions to do so can be found on the Clojure Assembla Wiki.

Once Leiningen is installed, use it to create a new Clojure project:

```bash
$ lein new hello-www
$ cd hello-www
```

This will create a basic project skeleton we can build upon. Update `project.clj` in the current directory to include Compojure 0.4.1 and the Ring Jetty adapter as dependencies:

```clojure
(defproject hello-www "1.0.0-SNAPSHOT"
  :description "A Compojure 'Hello World' application"
  :dependencies [[org.clojure/clojure "1.1.0"]
                 [org.clojure/clojure-contrib "1.1.0"]
                 [compojure "0.4.1"]
                 [ring/ring-jetty-adapter "0.2.6"]])
```

Next, use Leiningen to download the project dependencies for you:

```bash
$ lein deps
```

Now you’re ready to write the application. Put the following code into src/hello-www/core.clj:

```clojure
(ns hello-www.core
  (:use compojure.core)
  (:require [compojure.route :as route]))

(defroutes example
  (GET "/" [] "<h1>Hello World Wide Web!</h1>")
  (route/not-found "Page not found"))
```

To run this application, start a REPL using Leiningen

```bash
$ lein repl
```

Then pass your routes to the Jetty adapter.

```clojure
=> (use 'ring.adapter.jetty)
=> (use 'hello-www.core)
=> (run-jetty example {:port 8080})
```

Visit [[http://localhost:8080]] to see the results. If all has gone well, you’ll see your "Hello World" page.