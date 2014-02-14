The simplest way to get started with Compojure is to use [[Leiningen|http://github.com/technomancy/leiningen]], the standard Clojure build tool. If you haven't already, download and install Leiningen. You'll need Leiningen 2.0.0 or later for these next instructions to work.

Next, create a new project using the "compojure" template:

```bash
lein new compojure hello-world
```

This will create a basic project skeleton containing a minimal web application.

You can now change into the project directory, and start a development server using Leiningen:

```bash
cd hello-world
lein ring server-headless
```

The dev server will start up on port 5000. If you make a change to one of the source files, it will automatically reload it for you.