# Streamy Domain Model — Advanced UML Class Diagram

This document enriches the Streamy domain model with inheritance, relationship labels, multiplicity, and interactive hyperlinks.

## Step 1: Title and Generalizations (Inheritance)

Generalization (`--|>`) demonstrates that child entities are specialized subtypes of a parent entity.

```mermaid
---
title: Streamy Domain Model
---
classDiagram
    TVShow --|> Title
    Short --|> Title
    Film --|> Title
    Title --|> Genre
    Title *-- Season
    Title *-- Review
    Title o-- Actor
    Season *-- Episode
```

## Step 2: Relationship Labels and Directional Association

Clarify how entities interact by appending a colon `:` followed by a description. Use `-->` for a directional association where only one entity retains a reference to the other.

```mermaid
---
title: Streamy Domain Model
---
classDiagram
    Title --|> Genre : is associated with
    Title *-- Season : has
    Title *-- Review : has
    Title o-- Actor : features
    TVShow --|> Title : implements
    Short --|> Title : implements
    Film --|> Title : implements
    Viewer --> Title : watches
    Season *-- Review : has
    Season *-- Episode : contains
    Episode *-- Review : has
```

## Step 3: Multiplicity (Cardinality)

Multiplicity denotes numerical bounds (e.g., "1", "0..*", "1..*").

```mermaid
---
title: Streamy Domain Model
---
classDiagram
    Title "1" -- "1..*" Genre : is associated with
    Title "1" -- "0..*" Season : has
    Title "1" -- "0..*" Review : has
    Title "0..*" o-- "1..*" Actor : features
    TVShow --> Title : implements
    Short --> Title : implements
    Film --> Title : implements
    Viewer "0..*" -- "0..*" Title : watches
    Season "1" -- "0..*" Review : has
    Season "1" -- "1..*" Episode : contains
    Episode "1" -- "0..*" Review : has
```

## Step 4: Readability and External Links

Expand node height with `\n\n` and attach clickable URLs using the `link` keyword.

```mermaid
---
title: Streamy Domain Model
---
classDiagram
    Title : \n\n
    link Title "https://example.com/titles" _blank
    Title "1" -- "1..*" Genre : is associated with
    Title "1" -- "0..*" Season : has
    Title "1" -- "0..*" Review : has
    Title "0..*" o-- "1..*" Actor : features
    TVShow --> Title : implements
    Short --> Title : implements
    Film --> Title : implements
    Viewer "0..*" -- "0..*" Title : watches
    Season "1" -- "0..*" Review : has
    Season "1" -- "1..*" Episode : contains
    Episode "1" -- "0..*" Review : has
```