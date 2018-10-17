---
title: 'Swagger, Jersey2 and embedded Jetty &#8211; without strings'
author: admin
layout: post
permalink: /2015/04/swagger-jersey2-jetty/
videourl:
  - 
shareaholic_disable_recommendations:
  - 1
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
I remembered having used a cool framework for self-documenting APIs called <a href="http://swagger.io/" title="Swagger" target="_blank">Swagger</a> on a recent contract I was working on. Swagger is brilliant, easy and intuitive. Or so I thought.

What should have been a simple &#8220;point-click-run&#8221; in my head, turned out to be a bit more painful. I couldn&#8217;t find a Swagger example where there was no Spring MVC or web.xml dependencies. I just wanted it to run in a self-contained JAR using embedded Jetty &#8211; a clean package that runs anywhere. I found several examples using Spring, and it seemed excessive. I don&#8217;t want Spring. There were several using web.xml. I don&#8217;t want to configure an XML. I just want to write Jersey APIs and have them *just run*.

After running through several examples <a href="https://github.com/swagger-api/swagger-core" target="_blank">here</a> (Which are useful, just not for me), <a href="http://blog.randmind.org/post/embedded_jetty_jersey_swagger.html" target="_blank">here</a> (Though I didn&#8217;t really know what was happening&#8230;) and <a href="http://java.dzone.com/articles/swagger-make-developers-love" target="_blank">here</a>, I finally gave up and went to the <a href="irc://freenode.net:6666/#swagger" target="_blank">Swagger IRC channel on freenode</a>. I found **ron** and **tonytam** really helpful in fixing my problems.

To set up Swagger, you need to do the following:  
1. Annotate your Jersey resources with Swagger annotations. This page <a href="java.dzone.com/articles/swagger-make-developers-love" target="_blank">here</a> should get you started.  
2. Copy the contents of the dist folder from <a href="https://github.com/swagger-api/swagger-ui" target="_blank">here</a> to a folder called **webapp** in your **resources** folder.  
[<img src="http://caffinc.com/wp-content/uploads/2015/04/webapp.png?fit=202%2C203" alt="Swagger UI" class="aligncenter size-full wp-image-51" data-recalc-dims="1" />][1]  
3. Add the following code to somewhere in your application, ensuring it runs during start up (Main method is a great place). This sets up Swagger&#8217;s info and code scanning.  
`        BeanConfig beanConfig = new BeanConfig();<br />
        beanConfig.setVersion("1.0.0");<br />
        beanConfig.setResourcePackage(ResourceClass.class.getPackage().getName());<br />
        beanConfig.setScan(true);<br />
        beanConfig.setBasePath("/");<br />
        beanConfig.setDescription("Description goes here");<br />
        beanConfig.setTitle("Topic goes here");<br />
`  
Replace `ResourceClass.class` with your Jersey Resource class.  
4. Add your resources and the Swagger JAX-RS resources to Jetty. This serves up your APIs and the swagger.json file that the Swagger UI uses.  
`        ResourceConfig resourceConfig = new ResourceConfig();<br />
        resourceConfig.packages(ResourceClass.class.getPackage().getName(), "com.wordnik.swagger.jaxrs.listing");<br />
        ServletContainer servletContainer = new ServletContainer(resourceConfig);<br />
        ServletHolder entityBrowser = new ServletHolder(servletContainer);<br />
        ServletContextHandler entityBrowserContext = new ServletContextHandler(ServletContextHandler.SESSIONS);<br />
        entityBrowserContext.setContextPath("/");<br />
        entityBrowserContext.addServlet(entityBrowser, "/*");<br />
`  
Replace `ResourceClass.class` with your Jersey Resource class. `com.wordnik.swagger.jaxrs.listing` loads up Swagger&#8217;s resources, namely `com.wordnik.swagger.jaxrs.listing.ApiListingResource` and `com.wordnik.swagger.jaxrs.listing.SwaggerSerializers`. Add this `entityBrowserContext` to your Jetty server.  
This should now serve up <a href="http://localhost:9999/swagger.json" target="_blank">http://localhost:9999/swagger.json</a>

Start your application using the main class, and everything should work as planned. You should see this when you open <a href="http://localhost:9999/docs" target="_blank">http://localhost:9999/docs</a>:  
[<img src="http://caffinc.com/wp-content/uploads/2015/04/swagger-300x140.png?fit=300%2C140" alt="Swagger UI" class="aligncenter size-medium wp-image-48" data-recalc-dims="1" />][2]

**TL;DR**  
I get annoyed when I have to read a lot of text for something I want quick. I also hate it when there are no code samples that I can run, and dependencies aren&#8217;t mentioned correctly and all that stuff. So here you go:  
Get my Github repository from <a href="https://github.com/SriramKeerthi/swagger-jersey2-jetty" target="_blank">here</a>.  
Follow the <a href="https://github.com/SriramKeerthi/swagger-jersey2-jetty/blob/master/README.md" target="_blank">readme.md</a> and look at the code, which should get you started on how to set up a Spring/web.xml free Jersey2 and Swagger on embedded Jetty!

 [1]: http://caffinc.com/wp-content/uploads/2015/04/webapp.png
 [2]: http://caffinc.com/wp-content/uploads/2015/04/swagger.png
