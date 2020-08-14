---
layout: single
title: "Using Elastic APM with Scalatra"
date: 2020-08-14 00:47:00
tags: [scala, elasticsearch]

excerpt: "And getting precise transaction names."

imgDir: /assets/img/elasticapmscalatra
---

Scalatra and Elastic APM are great. Since Elastic APM's Java agent [supports the servlet API](https://www.elastic.co/guide/en/apm/agent/java/current/supported-technologies-details.html#supported-app-servers), particularly Jetty, you can instrument a Scalatra app by just [slapping on the agent](https://www.elastic.co/guide/en/apm/agent/java/current/setup-javaagent.html).

But you may notice the resulting transaction names have only the request's method and servlet's name. For example, all `GET`s in `HealthServlet` will fall under the transaction name `HealthServlet#doGet`:

![HealthServlet transaction names][img-do-get]

This is much better than nothing, but if you've got multiple routes in a single servlet, you may wish to disambiguate them.

Fortunately, getting more precise transaction names isn't too hard. By extending `ScalatraServlet`, you can hook into your routes and set a unique transaction name for each. Observe:

```
import co.elastic.apm.api.ElasticApm
import org.scalatra.{PathPatternRouteMatcher, RailsRouteMatcher, RegexRouteMatcher, Route, RouteTransformer, ScalatraServlet, SinatraRouteMatcher}

trait ScalatraApmServlet extends ScalatraServlet {
  /**
   * Calculate the route portion of an APM transaction name.
   *
   * We ignore a couple matchers that don't have obvious text representations. This should be fine for most purposes.
   *
   * @param transformers The route's transformers.
   * @return Transaction name path.
   */
  private def getApmTransactionPath(transformers: Seq[RouteTransformer]): String = {
    val transformersWithPath = transformers.filter {
      case _: PathPatternRouteMatcher => true
      case _: RailsRouteMatcher => true
      case _: RegexRouteMatcher => true
      case _: SinatraRouteMatcher => true
      case _ => false
    }

    val concatenated = transformersWithPath.map(_.toString).mkString("; ")
    if (concatenated != "/") {
      concatenated
    } else {
      ""
    }
  }

  private def wrapWithApmTransactionName(transactionPath: String, action: => Any): Any = {
    ElasticApm.currentTransaction.setName(s"${request.getMethod} ${request.getServletPath}${transactionPath}")

    action
  }

  override def get(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.get(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
  override def post(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.post(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
  override def put(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.put(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
  override def delete(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.delete(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
  override def options(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.options(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
  override def head(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.head(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
  override def patch(transformers: RouteTransformer*)(action: => Any): Route = {
    val transactionPath = getApmTransactionPath(transformers)
    super.patch(transformers: _*) { wrapWithApmTransactionName(transactionPath, action) }
  }
}
```

By making your servlets extend `ScalatraApmServlet` instead of `ScalatraServlet`, their routes will be distinguished in APM.

This works with Scalatra 2.7.0. I imagine it's prone to breaking with major Scalatra releases, but hope it won't be too hard to adapt when that happens.


[img-do-get]: {{ page.imgDir }}/doget.png
