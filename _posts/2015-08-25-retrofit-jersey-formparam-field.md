---
title: POST call to Jersey/Jax-rs endpoints with @FormParam using Retrofit @Field
author: admin
layout: post
permalink: /2015/08/retrofit-jersey-formparam-field/
videourl:
  - 
shareaholic_disable_recommendations:
  - 1
categories:
  - API
  - Java
  - Jetty
tags:
  - field
  - formparam
  - jaxrs
  - jersey
  - multipart
  - retrofit
---
As part of testing my endpoints, I use [Retrofit][1]. It is brilliant to say the least. But finding documentation on it can be a little tricky.

My endpoints had several parameters annotated with the `@FormParam` annotation. When I tried to write an equivalent method in the Retrofit interface, I didn&#8217;t know what to annotate the fields with.

API (Ignore the `Swagger annotations (ApiParam)`):

    @POST 
    @Path ("/{category}/{type}")
    @ApiOperation (value = "Upsert", notes = "Upserts data")
    @Produces (MediaType.APPLICATION_JSON) 
    public Response upsertData(
      @HeaderParam ("authorization") final String authorizationHeader,
      @ApiParam (value = "Category to upsert", required = true) @PathParam (value = "category") final String category,
      @ApiParam (value = "Type to upsert", required = true) @PathParam (value = "type") final String type,
      @ApiParam (value = "Data", required = true) @FormParam (value = "data") final String data )
    {
        // Cutting things down to the bare necessities...
    }
    

I tried the `@Field` annotations:

    @POST ("/{category}/{type}") Response upsertData( 
      @Path (value = "category") final String category, 
      @Path (value = "type") final String type,
      @Field (value = "data") final String data);
    

I got this exception:

    retrofit.RetrofitError: Client.upsertData: @Field parameters can only be used with form encoding. (parameter #3)
        at retrofit.RetrofitError.unexpectedError(RetrofitError.java:44)
        at retrofit.RestAdapter$RestHandler.invokeRequest(RestAdapter.java:400)
        at retrofit.RestAdapter$RestHandler.invoke(RestAdapter.java:240)
        at com.inferlytics.databrowser.helper.$Proxy6.upsertTrait(Unknown Source)
        ...
    Caused by: java.lang.IllegalArgumentException: Client.upsertTrait: @Field parameters can only be used with form encoding. (parameter #3)
        at retrofit.RestMethodInfo.methodError(RestMethodInfo.java:107)
        at retrofit.RestMethodInfo.parameterError(RestMethodInfo.java:111)
        at retrofit.RestMethodInfo.parseParameters(RestMethodInfo.java:356)
        at retrofit.RestMethodInfo.init(RestMethodInfo.java:118)
        at retrofit.RestAdapter$RestHandler.invokeRequest(RestAdapter.java:294)
        ... 32 more
    

I tried to change the `@Field` annotations to `@Part` and added a `@Multipart` encoding. This time my server complained:

    javax.servlet.ServletException: java.lang.IllegalStateException: The @FormParam is utilized when the content type of the request entity is not application/x-www-form-urlencoded
        at org.glassfish.jersey.servlet.WebComponent.service(WebComponent.java:423)
        at org.glassfish.jersey.servlet.ServletContainer.service(ServletContainer.java:386)
    

As my endpoint didn&#8217;t have a problem when called correctly, I knew the problem was my client. I finally realized that I was missing the `@FormUrlEncoded` annotation. I added it:

    @POST ("/{category}/{type}") @FormUrlEncoded Response upsertData( 
      @Path (value = "category") final String category, 
      @Path (value = "type") final String type,
      @Field (value = "data") final String data);
    

Things worked perfectly this time around! I hope this helps people who were stuck with something similar. I recommend Retrofit to everyone!

 [1]: http://square.github.io/retrofit/