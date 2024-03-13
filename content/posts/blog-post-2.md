---
title: "Code Highlighting"
date: 2021-12-18T11:10:36+08:00
draft: false
language: en
featured_image: ../assets/images/featured/featured-img-placeholder.png
summary: Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed cursus, odio nec venenatis lacinia, lacus lectus varius nisi, in tristique mi purus ut libero.
description: Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed cursus, odio nec venenatis lacinia, lacus lectus varius nisi, in tristique mi purus ut libero. Vestibulum vel convallis felis. Ut finibus lorem vestibulum lobortis rhoncus.
author: TailBliss
authorimage: ../assets/images/global/author.webp
categories: blog
tags: "blog posts"
---
__Advertisement :smile:__

- __[pica](https://nodeca.github.io/pica/demo/)__ - high quality and fast image
  resize in browser.
- __[babelfish](https://github.com/nodeca/babelfish/)__ - developer friendly
  i18n with plurals support and easy syntax.

You will like those projects!

---

# h1 Heading :blush:

```scala title="BackendServerConf.scala" hl_lines="22 29-32"
package com.lovindata
package config

import cats.effect.IO
import cats.implicits._
import com.comcast.ip4s._
import features.text.TextCtrl
import org.http4s.ember.server.EmberServerBuilder
import org.http4s.implicits._
import shared.BackendException.ServerInternalErrorException
import sttp.tapir.server.http4s.Http4sServerInterpreter
import sttp.tapir.swagger.bundle.SwaggerInterpreter

object BackendServerConf {
  def start: IO[Unit] = for {
    port <- IO.fromOption(Port.fromInt(EnvLoaderConf.backendPort))(
              ServerInternalErrorException(s"Not processable port number ${EnvLoaderConf.backendPort}."))
    _    <- EmberServerBuilder
              .default[IO]
              .withHost(ipv4"0.0.0.0")        // Accept connections from any available network interface
              .withPort(port)                 // On port 8080
              .withHttpApp(allRts.orNotFound) // Link all routes to the backend server
              .build
              .use(_ => IO.never)
              .start
              .void
  } yield ()

  private val docsEpt = // Merge all endpoints as a fully usable OpenAPI doc
    SwaggerInterpreter().fromEndpoints[IO](TextCtrl.endpoints, "Backend", "1.0")
  private val allRts  = // Serve the OpenAPI doc & all the other routes
    Http4sServerInterpreter[IO]().toRoutes(docsEpt) <+> TextCtrl.routes
}
```