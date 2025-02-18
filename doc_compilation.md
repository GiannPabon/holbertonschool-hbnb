```markdown
# Documentation Compilation

## Table of Contents
1. [Introduction](#introduction)  
2. [High-Level Diagram](#high-level-diagram)  
3. [Business Logic Diagram](#business-logic-diagram)  
4. [Sequence Diagrams](#sequence-diagrams)  
   1. [User Registration](#1-user-registration)  
   2. [Place Creation](#2-place-creation)  
   3. [Review Submission](#3-review-submission)  
   4. [Fetching a List of Places](#4-fetching-a-list-of-places)  
5. [Conclusion](#conclusion)

---

## Introduction

This document **combines all UML diagrams** required by the **HBnB - UML** project:
- **High-Level Package Diagram** (3-layer approach + Facade)  
- **Detailed Class Diagram** (business logic for `User`, `Place`, `Review`, `Amenity`)  
- **Sequence Diagrams** (user registration, place creation, review submission, listing places)

---

## High-Level Diagram

Below is the **raw code** for the **high-level package diagram**. Copy/paste into a Mermaid-capable Markdown viewer or use an online Mermaid editor.

```markdown
# High-Level Package Diagram

```mermaid
classDiagram
    class PresentationLayer {
        <<Package>>
        API Endpoints
        Services
        +userRegister()
        +createPlace()
        +reviewPlace()
        +fetchPlaces()
    }

    PresentationLayer --> BusinessLogicLayer : Uses Facade

    class BusinessLogicLayer {
        <<Package>>
        User
        Place
        Review
        Amenity
        +validateData()
        +processRequests()
    }

    BusinessLogicLayer --> PersistenceLayer : Database Operations

    class PersistenceLayer {
        <<Package>>
        Database Integration
        +save()
        +update()
        +delete()
        +query()
    }
```
```

### Explanation
- **PresentationLayer**: Handles routes, services, and user interactions.  
- **BusinessLogicLayer**: Houses core domain logic and provides a facade for simpler calls.  
- **PersistenceLayer**: Performs database operations (save, update, delete, query).

---

## Business Logic Diagram

Below is the **raw code** for the **business logic class diagram** showing the main entities.

```markdown
# Business Logic Diagram

```mermaid
classDiagram
class User {
    - uuid4 id
    - string first_name
    - string last_name
    - string email
    - string password
    - bool is_admin
    - datetime created_at
    - datetime updated_at

    + register()
    + updateProfile()
    + deleteUser()
}

class Place {
    - uuid4 id
    - string title
    - string description
    - float price
    - float latitude
    - float longitude
    - datetime created_at
    - datetime updated_at

    + create()
    + update()
    + delete()
    + addAmenity()
}

class Review {
    - uuid4 id
    - int rating
    - string comment
    - datetime created_at
    - datetime updated_at

    + create()
    + update()
    + delete()
}

class Amenity {
    - uuid4 id
    - string name
    - string description
    - datetime created_at
    - datetime updated_at

    + create()
    + update()
    + delete()
}

User "1" --> "many" Place : owns
Place "1" --> "many" Review : has
User "1" --> "many" Review : writes
Place "many" -- "many" Amenity : associated with
```
```

### Notes
- **User**: Unique ID, timestamps, plus `is_admin` for privileges.  
- **Place**: Tied to a `User` as its owner, can have multiple `Amenities`.  
- **Review**: Associated with a single `User` and `Place`.  
- **Amenity**: Many-to-many with `Place`.

---

## Sequence Diagrams

Below are **four** sequence diagrams covering key API workflows: **User Registration**, **Place Creation**, **Review Submission**, and **Fetching Places**.

### 1. User Registration

```markdown
# sequence_diagram_register.md

```mermaid
sequenceDiagram
    participant Client
    participant API as Presentation Layer
    participant Facade as Business Logic (Facade)
    participant DB as Persistence Layer

    Client->>API: POST /register (user data)
    API->>Facade: register_user(data)
    Facade->>DB: INSERT new user record
    DB-->>Facade: success or error
    Facade-->>API: user object or error
    API-->>Client: HTTP 201 or 400/500
```
```

### 2. Place Creation

```markdown
# sequence_diagram_place.md

```mermaid
sequenceDiagram
    participant Client
    participant API as Presentation Layer
    participant Facade as Business Logic (Facade)
    participant DB as Persistence Layer

    Client->>API: POST /places (place details)
    API->>Facade: create_place(details)
    Facade->>DB: INSERT place record
    DB-->>Facade: success or error
    Facade-->>API: place object or error
    API-->>Client: HTTP 201 or error
```
```

### 3. Review Submission

```markdown
# sequence_diagram_review.md

```mermaid
sequenceDiagram
    participant Client
    participant API as Presentation Layer
    participant Facade as Business Logic (Facade)
    participant DB as Persistence Layer

    Client->>API: POST /places/{id}/reviews
    API->>Facade: create_review(place_id, user_id, rating, comment)
    Facade->>DB: INSERT review (with references to place_id, user_id)
    DB-->>Facade: success or error
    Facade-->>API: review object or error
    API-->>Client: HTTP 201 or error
```
```

### 4. Fetching a List of Places

```markdown
# sequence_diagram_fetch.md

```mermaid
sequenceDiagram
    participant Client
    participant API as Presentation Layer
    participant Facade as Business Logic (Facade)
    participant DB as Persistence Layer

    Client->>API: GET /places?criteria=...
    API->>Facade: list_places(criteria)
    Facade->>DB: SELECT places matching criteria
    DB-->>Facade: list of place records
    Facade-->>API: list of place objects
    API-->>Client: HTTP 200 + JSON
```
```

---

## Conclusion

All diagrams are **now compiled** into this single file for easy reference. They outline:

1. A **3-Layer Architecture** (Presentation, Business Logic, Persistence) with a Facade.  
2. **Entities** and **relationships** (`User`, `Place`, `Review`, `Amenity`).  
3. **Workflow** for typical API interactions (user registration, place creation, reviews, and fetching places).

These UML artifacts serve as the **blueprint** for the HBnB application’s structure and data flows, guiding both future implementation and documentation.
