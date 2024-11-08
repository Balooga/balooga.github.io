+++
date = '2018-02-22T23:36:56-07:00'
draft = false 
title = 'Choosing between RPC and Rest for API Designs'
+++

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

Stack Overflow overflows with debates about what constitutes good API design, what is or isn't considered RESTful, and if RPC is a relic of the past or the way of the future (again). Should we mangle an API to expose a made-up "resource" just so there is a thing a HTTP method can operate on to conform to a definition of "RESTful"? Even when there exists no such resource in the back-end? Must the operations a service performs be viewed through the lense of the methods of the HTTP application layer? 

Or, we can accept that RPC can simplify the designs for many real world use cases that cannot be adequately captured as a resource operated on by the HTTP `PUT`, `GET`, `DELETE`, and `UPDATE` methods.

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
