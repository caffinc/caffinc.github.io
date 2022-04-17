---
title: 'Swagger, Jersey2 and embedded Jetty - without strings'
author: admin
layout: post
permalink: /2015/04/swagger-jersey2-jetty/
categories:
  - API
  - Java
  - Swagger
tags:
  - api
  - embedded jetty
  - java
  - jersey
  - jersey2
  - jetty
  - spring
  - swagger
  - web.xml
---
I remembered having used a cool framework for self-documenting APIs called [Swagger](http://swagger.io/) on a recent contract I was working on. Swagger is brilliant, easy and intuitive. Or so I thought.

What should have been a simple 'point-click-run' in my head, turned out to be a bit more painful. I couldn't find a Swagger example where there was no Spring MVC or web.xml dependencies. I just wanted it to run in a self-contained JAR using embedded Jetty - a clean package that runs anywhere. I found several examples using Spring, and it seemed excessive. I don't want Spring. There were several using web.xml. I don't want to configure an XML. I just want to write Jersey APIs and have them *just run*.

After running through several examples [here](https://github.com/swagger-api/swagger-core) (Which are useful, just not for me), [here](http://blog.randmind.org/post/embedded_jetty_jersey_swagger.html) (Though I didn't really know what was happening!) and [here](http://java.dzone.com/articles/swagger-make-developers-love), I finally gave up and went to the [Swagger IRC channel on freenode](irc://freenode.net:6666/#swagger). I found **ron** and **tonytam** really helpful in fixing my problems.

To set up Swagger, you need to do the following:  
1. Annotate your Jersey resources with Swagger annotations. This page [here](java.dzone.com/articles/swagger-make-developers-love) should get you started.  
2. Copy the contents of the dist folder from [here](https://github.com/swagger-api/swagger-ui) to a folder called **webapp** in your **resources** folder.  
![Swagger UI](/assets/images/2015/04/webapp.png)
3. Add the following code to somewhere in your application, ensuring it runs during start up (Main method is a great place). This sets up Swagger's info and code scanning.  
```
        BeanConfig beanConfig = new BeanConfig();<br />
        beanConfig.setVersion("1.0.0");<br />
        beanConfig.setResourcePackage(ResourceClass.class.getPackage().getName());<br />
        beanConfig.setScan(true);<br />
        beanConfig.setBasePath("/");<br />
        beanConfig.setDescription("Description goes here");<br />
        beanConfig.setTitle("Topic goes here");<br />
```
Replace `ResourceClass.class` with your Jersey Resource class.  
4. Add your resources and the Swagger JAX-RS resources to Jetty. This serves up your APIs and the swagger.json file that the Swagger UI uses.  
```
        ResourceConfig resourceConfig = new ResourceConfig();<br />
        resourceConfig.packages(ResourceClass.class.getPackage().getName(), "com.wordnik.swagger.jaxrs.listing");<br />
        ServletContainer servletContainer = new ServletContainer(resourceConfig);<br />
        ServletHolder entityBrowser = new ServletHolder(servletContainer);<br />
        ServletContextHandler entityBrowserContext = new ServletContextHandler(ServletContextHandler.SESSIONS);<br />
        entityBrowserContext.setContextPath("/");<br />
        entityBrowserContext.addServlet(entityBrowser, "/*");<br />
```
Replace `ResourceClass.class` with your Jersey Resource class. `com.wordnik.swagger.jaxrs.listing` loads up Swagger's resources, namely `com.wordnik.swagger.jaxrs.listing.ApiListingResource` and `com.wordnik.swagger.jaxrs.listing.SwaggerSerializers`. Add this `entityBrowserContext` to your Jetty server.  
This should now serve up [http://localhost:9999/swagger.json](http://localhost:9999/swagger.json)

Start your application using the main class, and everything should work as planned. You should see this when you open [http://localhost:9999/docs](http://localhost:9999/docs):  
![Swagger UI](/assets/images/2015/04/swagger.png)

**TL;DR**  
I get annoyed when I have to read a lot of text for something I want quick. I also hate it when there are no code samples that I can run, and dependencies aren't mentioned correctly and all that stuff. So here you go:
1. Get my Github repository [here](https://github.com/SriramKeerthi/swagger-jersey2-jetty)
2. Follow the [README.md](https://github.com/SriramKeerthi/swagger-jersey2-jetty/blob/master/README.md) and look at the code, which should get you started on how to set up a `Spring/web.xml` free `Jersey2` and `Swagger` on embedded `Jetty`!
