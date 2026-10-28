# C4 Model: Component Diagram

This document zooms into the **Component** level (Level 3) of the C4 model for the Listing Service, modeling the internal structural building blocks (controllers, services, repositories) inside the Web Application container.

## Complete C4 Component Diagram with Styling

```mermaid
---
title: "Listing Service C4 Model: Component Diagram"
---
flowchart TD
    classDef container fill:#1168bd,stroke:#0b4884,color:#ffffff
    classDef externalSystem fill:#666666,stroke:#0b4884,color:#ffffff
    classDef component fill:#85bbf0,stroke:#5d82a8,color:#000000

    Browser["🌐 Browser\n[Web Browser]\n\nUsed by a user to browse\nthe website"]
    MA["📱 Application\n[Xamarin Application]\n\nAllows members to view and review\ntitles from their mobile devices"]
    R[("💾 In-Memory Cache\n[Redis]\n\nTitles and their reviews\nare cached")]
    K["📨 Message Broker\n[Kafka]\n\nImportant domain events\nare published to Kafka"]
    TS["📦 Title Service\n[Software System]\n\nProvides an API to retrieve\ntitle information"]
    RS["📝 Review Service\n[Software System]\n\nProvides an API to retrieve\nand submit reviews"]
    SS["🔍 Search Service\n[Software System]\n\nProvides an API to search\nfor titles"]

    subgraph listing-service [Listing Service Web Application]
        TCont["Title Controller\n[ASP.NET MVC Controller]\n\nAllows users to view details\nabout titles"]
        SCont["Search Controller\n[ASP.NET MVC Controller]\n\nAllows users to search\nfor titles"]
        RCont["Review Controller\n[ASP.NET MVC Controller]\n\nAllows users to read and\nwrite reviews"]

        TComp["Title Component\n[ASP.NET Namespace]\n\nProvides information on titles,\nretrieves info from Title Service\nand caches titles"]
        SComp["Search Component\n[ASP.NET Namespace]\n\nSearches titles using the\nSearch Service"]
        RComp["Review Component\n[ASP.NET Namespace]\n\nProvides review info,\nsubmits new reviews\nand publishes domain events"]

        TCont --> TComp
        RCont --> TComp
        RCont --> RComp
        SCont --> SComp
    end

    Browser -->|"Submits requests to [HTTPS]"| TCont
    Browser -->|"Submits requests to [HTTPS]"| SCont
    Browser -->|"Submits requests to [HTTPS]"| RCont
    MA -->|"Submits requests to [HTTPS]"| TCont
    MA -->|"Submits requests to [HTTPS]"| SCont
    MA -->|"Submits requests to [HTTPS]"| RCont

    TComp --> TS
    TComp --> R
    RComp --> R
    RComp --> K
    RComp --> RS
    SComp --> SS

    class MA,Browser container
    class SS,RS,TS,K externalSystem
    class RComp,SComp,TComp,RCont,SCont,TCont component

    style listing-service fill:none,stroke:#CCC,stroke-width:2px,stroke-dasharray:5 5
```