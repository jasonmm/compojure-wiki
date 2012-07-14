The simplest way to get started with Compojure is to use [[Leiningen|http://github.com/technomancy/leiningen]], the standard Clojure build tool. If you haven't already, download and install Leiningen.

If you're using Leiningen 1, you should first run the following commands:

```bash
lein plugin install lein-newnew 0.2.6
lein plugin install compojure/lein-template 0.2.0
```

If you're using the Leiningen 2 preview release, do not run these commands. The plugins will be automatically loaded without you needing to do anything.

Next, create a new project using the "compojure" template:

```bash
lein new compojure hello-world
```

This will create a basic project skeleton containing a minimal web application.

You can now change into the project directory, and start a development server using Leiningen:

```bash
cd hello-world
lein ring server
```

The dev server will find a free port and open a browser window for you. If you make a change to one of the source files, it will automatically reload it for you.