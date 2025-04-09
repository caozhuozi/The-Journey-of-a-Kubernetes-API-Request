# The Journey of a Kubernetes API Request: Unveiling the API Server the Hard Way

> [!NOTE]
> This tutorial is still in its early stages. The content is subject to frequent updates and changes.

Kubernetes apimachinery is tough.
As the Kubernetes apiserver has evolved to its current state, it has become really hard for beginners—even those with lots of enthusiasm—to figure out
how it works behind the scenes.

There is limited content available that delves into the details of the apiserver. I think one reason for this is its inherent complexity,
which makes it challenging to explain clearly in just a few blog posts. Additionally, the payoff for gaining a deep understanding of the apiserver
is relatively small. The majority of Kubernetes developers tend to focus on custom resource development, which is why there’s more content available on that topic.
We must acknowledge that custom resources are great and efficient, serving as the primary method for extending the Kubernetes API within the community.
Admittedly, there are fewer scenarios where understanding the apiserver is essential compared to developing Custom Resource Definitions (CRDs).
At present, the primary case is when you’re building an aggregation layer to extend the apiserver—and even that isn’t a common need.
This raises the question: why should we concern ourselves with the inner workings of the apiserver?
For me, the real motivation boils down to a love for Kubernetes and a deep curiosity about how it works under the hood.
If you share this same enthusiasm, I hope this project will prove valuable to you.

So far, all the resources I’ve collected that dive into the specifics of the apiserver include:

However, much of this material only scratches the surface and doesn’t provide enough detail. As I mentioned earlier, thoroughly explaining these details is a difficult task.

In this tutorial, we'll take a deep dive into the journey of a Kubernetes API request
—tracing the path from when a user submits a request to the Kubernetes apiserver, all the way to the (etcd) storage layer.
Along the way, we’ll explore the internal mechanics of the Kubernetes API server and gain a deeper understanding of how it processes requests.

For Go developers, the "journey/lifecycle of a request" in a HTTP server typically refers
to how a request flows through a chain of `http.Handler` (*middlewares*).

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
This tutorial aims to bridge the gap between the familiar `http.Handler` concept of Go and the complete workings of the Kubernetes apiserver.
