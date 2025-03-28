# BambangShop Publisher App

Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project

In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:

1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment

1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable | type | description |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)

- [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
- **STAGE 1: Implement models and repositories**
  - [x] Commit: `Create Subscriber model struct.`
  - [x] Commit: `Create Notification model struct.`
  - [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
  - [x] Commit: `Implement add function in Subscriber repository.`
  - [x] Commit: `Implement list_all function in Subscriber repository.`
  - [x] Commit: `Implement delete function in Subscriber repository.`
  - [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
- **STAGE 2: Implement services and controllers**
  - [x] Commit: `Create Notification service struct skeleton.`
  - [x] Commit: `Implement subscribe function in Notification service.`
  - [x] Commit: `Implement subscribe function in Notification controller.`
  - [x] Commit: `Implement unsubscribe function in Notification service.`
  - [x] Commit: `Implement unsubscribe function in Notification controller.`
  - [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
- **STAGE 3: Implement notification mechanism**
  - [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
  - [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
  - [x] Commit: `Implement publish function in Program service and Program controller.`
  - [x] Commit: `Edit Product service methods to call notify after create/delete.`
  - [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections

This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

> In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?
> In the context of the BambangShop case, a single Subscriber model struct is sufficient, and we do not necessarily need an interface (or trait in Rust). Here's why:

1. Observer Pattern in BambangShop: The Subscriber struct already encapsulates the behavior required for the Observer pattern, specifically the update method that sends notifications. Since all subscribers share the same behavior (sending HTTP POST requests), there is no need for polymorphism or multiple implementations of the update method.
2. Traits in Rust: Traits are useful when you need to define a common interface for multiple types that may have different implementations. In this case, there is only one type of Subscriber, and its behavior is uniform. Introducing a trait would add unnecessary complexity without providing additional benefits.
3. Simpler Design: Using a single struct keeps the design simple and avoids over-engineering. The current implementation is straightforward and aligns well with the requirements of the Observer pattern in this specific use case.

> id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?
> Using DashMap (a thread-safe map/dictionary) is necessary in this case. DashMap is the appropriate choice for ensuring uniqueness, providing efficient operations, and supporting thread-safe concurrent access, which are all essential for the requirements of this project.

> When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?
> The DashMap library is necessary for the SUBSCRIBERS static variable in this project, even with the Singleton pattern, because it ensures thread-safe, efficient, and concurrent access to the data. While the Singleton pattern guarantees a single instance of the variable, it does not inherently handle concurrent reads and writes, which are common in a web application like BambangShop. DashMap simplifies implementation by abstracting thread safety and providing constant-time operations for lookups, insertions, and deletions, avoiding the complexity and performance bottlenecks of manual synchronization mechanisms like Mutex. Thus, combining DashMap with the Singleton pattern ensures both correctness and scalability in a multi-threaded environment.

#### Reflection Publisher-2

> In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
> Separating "Service" and "Repository" from the "Model" in the MVC pattern is essential for maintaining clean architecture and adhering to the Single Responsibility Principle. The "Model" in MVC traditionally combines both data storage and business logic, but this can lead to tightly coupled code that is harder to maintain, test, and extend. By introducing a "Repository," we isolate data access logic (e.g., database operations), ensuring that the "Model" does not directly interact with the database. Similarly, the "Service" layer encapsulates business logic, keeping it separate from both the "Model" and the "Controller." This separation reduces code complexity, improves testability, and allows each layer to evolve independently. Without this separation, interactions between models like Program, Subscriber, and Notification would result in bloated models with overlapping responsibilities, making the codebase harder to manage and increasing the risk of bugs.

> What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?
> If we only use the Model without separating it into "Service" and "Repository," the interactions between models like Program, Subscriber, and Notification would significantly increase code complexity. The Model would need to handle not only data storage but also business logic and database operations, leading to tightly coupled and bloated code. For example, the Program model would need to directly manage subscriber notifications and database queries, mixing concerns and making the code harder to maintain. This lack of separation would also make testing more difficult, as each model would have overlapping responsibilities, requiring complex setups to test individual functionalities. Additionally, any changes to one part of the logic (e.g., notification handling) could inadvertently affect other parts, increasing the risk of bugs and reducing the flexibility to adapt or extend the system in the future.

> Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
> Yes, Postman is an incredibly helpful tool for testing and debugging REST APIs. It allows me to easily send HTTP requests (GET, POST, PUT, DELETE, etc.) to endpoints and view the responses, which is essential for verifying the functionality of the API during development. Features like the ability to save requests in collections help organize and reuse them for future testing. The environment variables feature is particularly useful for managing different configurations (e.g., development, staging, production).

#### Reflection Publisher-3
