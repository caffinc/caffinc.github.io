---
title: "Jersey Injection Source Error: Accepting user defined entities in your API"
author: admin
layout: post
permalink: /2016/04/jersey-injection/
categories:
  - Java
  - API
  - Jersey
tags:
  - java
  - jersey
  - api
  - injection
---
I was writing an API for one of the projects I was involved in. The system used Jersey 2, Jetty and Java. My job was to accept a custom entity object from the user as a body parameter in Jersey.

I wrote the following code:

	@POST
    @Path("/visual/{customerKey}")
    @Produces(MediaType.APPLICATION_JSON)
    public Response getVisuals(@PathParam("customerKey") final String customerKey, @FormParam("value") final SearchQuery query) {
    
I knew there was something wrong with this. But I wanted some way to capture the body of my POST call. I decided that `FormParam` was a valid guess. So I tried it out.

It blew up.

This was the error:

	WARNING: No injection source found for a parameter of type public javax.ws.rs.core.Response com.caffinc.dummy.resources.DummyResource.getVisuals(java.lang.String,com.caffinc.dummy.entities.SearchQuery) at index 1.

Oookay... that didn't work as planned. I searched for a way to do this and someone suggested to use the `@BeanParam` parameter instead. This involved some crazy amounts of annotations and still didn't seem like what I wanted. I was a bit annoyed that something this common was becoming impossible to use.

So I removed all annotations from the parameter:

	@POST
    @Path("/visual/{customerKey}")
    @Produces(MediaType.APPLICATION_JSON)
    public Response getVisuals(@PathParam("customerKey") final String customerKey, final SearchQuery query) {

Voila! That was it. It worked! If you want to capture the body of the POST in an entity, and the body is a JSON, this will do it! No messy annotations, no complex logic.

Hope this helps someone! Good luck with your project!
