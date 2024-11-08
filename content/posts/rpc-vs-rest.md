+++
date = '2018-02-22T23:36:56-07:00'
draft = false 
title = 'Choosing between RPC and Rest for API Designs'
+++
(0.1)

The following diagram illustrates my thinking when designing an API.

```goat
     ┌────────────┐                                                                                                                     
     │Rest or RPC?│                                                                                                                     
     └──────┬─────┘                                                                                                                     
  __________▽___________       _________________       __________________                                                               
 ╱                      ╲     ╱                 ╲     ╱                  ╲                 ┌────────────────┐                           
╱ Is the API both "safe" ╲___╱ and guaranteed to ╲___╱ even after you     ╲________________│If you say so...│                           
╲ and "idempotent"?      ╱yes╲ be always thus?   ╱yes╲ leave the company? ╱yes             └────────┬───────┘                           
 ╲______________________╱     ╲_________________╱     ╲__________________╱       ___________________▽____________________               
            │no                        │no                     │no              ╱                                        ╲              
            │                          │                       │               ╱ Is caching a requirement for performance ╲    ┌───────┐
            │                          │                       │              ╱  reasons? I.e. Subsequent requests can be  ╲___│Use GET│
            │                          │                       │              ╲  served from a Proxy or Managed cache      ╱yes└───────┘
            │                          │                       │               ╲ instead of the origin server             ╱             
            │                          │                       │                ╲________________________________________╱              
            │                          │                       │                                    │no                                 
            │                          │                       │                         ___________▽___________                        
            │                          │                       │                        ╱                       ╲    ┌───────┐          
            │                          │                       │                       ╱ Is the ability to       ╲___│Use GET│          
            │                          │                       │                       ╲ bookmark a requirement? ╱yes└───────┘          
            │                          │                       │                        ╲_______________________╱                       
            │                          │                       │                                    │no                                 
            └────────────────────────┬─┴───────────────────────┴────────────────────────────────────┘                                   
                                ┌────▽───┐                                                                                              
                                │Use POST│                                                                                              
                                └────────┘                                                                                              
```

Stack Overflow is flooded with debates on what constitutes good API design, what is or isn't considered RESTful, and if RPC is a relic of the past or the way of the future (again).

Lets begin with the definitive definition of REST from its creator;

> REST is not an architecture, but rather an architectural style. It is a set of constraints that, when adhered to, will induce a set of properties; most of those properties are believed to be beneficial for decentralized, network-based applications, while others are the negative trade-offs that can result from any design choice (any constraint implies that a designer’s space of choices is reduced). REST does not directly constrain the Web’s architecture. Rather, an application developer may choose to constrain an architecture in accordance with the REST style. There is no way to force adherence to the REST constraints, though some poorly considered applications might not work well without them.
-- [Reflections on the REST Architectural Style and “Principled Design of the Modern Web Architecture”, Roy T Fielding, et al.](https://research.google.com/pubs/archive/46310.pdf)

That's a mouthful. Few in the software community will have read that paper, or Roy Fielding's [original dissertation on REST](https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) -- I handn't for years. Most developers have read only what others have written second or third hand on sites like Stack Overflow and Reddit (I had for years). [Roy Fielding's Misappropriated REST Dissertation](https://twobithistory.org/2020/06/28/rest.html) gives a great assessment on how the software development community completely misunderstood REST. 

What is REST great for? Nothing beats the HTTP 1.1 protocol for allowing a web-server to communicate with any web browser, and it would be silly to use anything other than HTTP 1.1 for this. REST does not prescribe how to build APIs on top of HTTP 1.1. REST describes the architectural constraints one should follow when using HTTP 1.1 to build systems that work like the browser/web-server model.

If you are developing a web app or web service and your architectural constraints are the same as, or similar to those of a web browser and web-server, then you could to worse than follow the REST architectural style. Because the REST architectural style is really great for this use-case. 

However, there is no need to adhere to the REST style when building, for example, a native mobile app that communicates with a set of back-end services. Different constraints means there is no architectural reason, other than to claim that your API is "RESTful", to mangle an API to expose a made-up "resource" when there exists no such resource in the back-end, simply to have an object for the HTTP methods to operate on. 

We should accept that RPC simplifies the designs for many real world use cases that cannot be adequately captured as a resource operated on by the HTTP `PUT`, `GET`, `DELETE`, and `UPDATE` methods, and architect accordingly.

And then just use `POST`.

<!-- https://diagon.arthursonzogni.com/#Flowchart -->

<!-- "Rest or RPC?"; -->

<!-- if ("Is the API both \"safe\" and \"idempotent\"?") { -->
<!--   if ("and guaranteed to be always thus?") { -->
<!--     if ("even after you leave the company?") { -->
<!--       "If you say so..." -->

<!--       if ("Is caching a requirement for performance reasons? I.e. Subsequent requests can be served from a Proxy or Managed cache instead of the origin server") { -->
<!--        return "Use GET" -->
<!--       } -->

<!--      if ("Is the ability to bookmark a requirement?") { -->
<!--       return "Use GET" -->
<!--      } -->
<!--    } -->
<!-- } -->
<!-- } -->

<!-- "Use POST"; -->
