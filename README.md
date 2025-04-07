# The Journey of a Kubernetes API Request: Unveiling the API Server the Hard Way

> [!NOTE]
> This tutorial is still in its early stages. The content is subject to frequent updates and changes.

In this tutorial, we'll take a deep dive into the journey of a Kubernetes API request
—tracing the path from when a user submits a request to the Kubernetes apiserver, all the way to the (etcd) storage layer.
Along the way, we’ll explore the internal mechanics of the Kubernetes API server and gain a deeper understanding of how it processes requests.

For Go developers, the "journey/lifecycle of a request" in a HTTP server typically refers to how a request flows through
a chain of `http.Handler` middlewares.

Here’s a typical HTTP handler in Go:

```go
func Handler(w http.ResponseWriter, r *http.Request) {
}
```

A typical middleware function in Go:

```go
func Middleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // do something before the next handler
        next.ServeHTTP(w, r)
        // do something after the next handler
    }
}
```

In the Kubernetes apiserver, the whole picture is more complex but still builds on this fundamental pattern.
This tutorial aims to bridge the gap between the familiar `http.Handler` concept and the complete workings
of the Kubernetes apiserver.
