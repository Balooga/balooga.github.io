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
  ___________▽____________       _________________       __________________                                                               
 ╱                        ╲     ╱                 ╲     ╱                  ╲                 ┌────────────────┐                           
╱ Is the request both      ╲___╱ and guaranteed to ╲___╱ even after you     ╲________________│If you say so...│                           
╲ "safe" and "idempotent"? ╱yes╲ be always thus?   ╱yes╲ leave the company? ╱yes             └────────┬───────┘                           
 ╲________________________╱     ╲_________________╱     ╲__________________╱       ___________________▽____________________               
             │no                         │no                     │no              ╱                                        ╲              
             │                           │                       │               ╱ Is caching a requirement for performance ╲    ┌───────┐
             │                           │                       │              ╱  reasons? I.e. Subsequent requests can be  ╲___│Use GET│
             │                           │                       │              ╲  served from a Proxy or Managed cache      ╱yes└───────┘
             │                           │                       │               ╲ instead of the origin server             ╱             
             │                           │                       │                ╲________________________________________╱              
             │                           │                       │                                    │no                                 
             │                           │                       │                         ___________▽___________                        
             │                           │                       │                        ╱                       ╲    ┌───────┐          
             │                           │                       │                       ╱ Is the ability to       ╲___│Use GET│          
             │                           │                       │                       ╲ bookmark a requirement? ╱yes└───────┘          
             │                           │                       │                        ╲_______________________╱                       
             │                           │                       │                                    │no                                 
             └────────────────────────┬──┴───────────────────────┴────────────────────────────────────┘                                   
                                 ┌────▽───┐                                                                                               
                                 │Use POST│                                                                                               
                                 └────────┘                                                                                               
```

Stack Overflow is flooded with debates on what constitutes good API design, what style is or isn't considered RESTful, and if RPC is a relic of the past or the way of the future (again).

Nothing beats HTTP 1.1 as the protocol that allows a web-server to communicate with any web browser, and it would be silly to use anything other than HTTP 1.1 for this.

If you are developing a web app or web service and your architectural constraints are the same as, or similar to those of a web browser and web-server, then you could to worse than follow the REST architectural style. Because the REST architectural style is really great for this use-case. See [Reflections on the REST Architectural Style and “Principled Design of the Modern Web Architecture”, Roy T Fielding, et al.](https://research.google.com/pubs/archive/46310.pdf). 

However, there is no need to adhere to the REST style if your web app or web service has a different set of architectural constraints to the browser/web-server model. Different constraints means there is no architectural reason, other than to claim that your API is "RESTful", to mangle an API to expose a made-up "resource" when there exists no such resource in the back-end, simply to have an object for the HTTP methods to operate on. 

Accept that RPC simplifies the designs for many real world use cases that cannot be adequately captured as a resource operated on by the HTTP `PUT`, `GET`, `DELETE`, and `UPDATE` methods, and architect accordingly.

<!-- https://diagon.arthursonzogni.com/#Flowchart -->

<!-- "Rest or RPC?"; -->

<!-- if ("Is the request both \"safe\" and \"idempotent\"?") { -->
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
