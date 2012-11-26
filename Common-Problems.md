This page lists some pitfalls that may not be obvious to users. Feel free to add your own!

### You have middleware that tries to read the `:body` of a request, but finds it empty

`:body` is an `InputStream`. It is mutable can only ever be read once. Anything that tries to read it after it has already been read will fail to do so. If you find it empty when it shouldn't be, the most likely cause is some other middleware higher in the stack reading it before it gets to you. One example of this is if you had two sets of routes each with `wrap-params` wrapped around them in the same stack. Only the first routes would have the params.