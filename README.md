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

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

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
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. For now, as BambangShop uses a single model struct, it suggests that all subscribers work the same way, hence the system doesn't need flexibility. So a single struct is enough. It will be a different case if we expect different types of subscribers or a variation of different behaviors when receiving updates.

2. Since id and url must be unique, DashMap is necessary because it checks uniqueness instantly and prevents duplicates whereas checking uniqueness in a vec requires iteration through all elements.

3. We still need DashMap as it is designed for safe concurrent access. We use it if multiple threads might access or modify the data simultaneously or if we need efficient, thread-safe read or write operations.

#### Reflection Publisher-2
1. We know that the Model handles business logic and data storage, this leads to the violations of the Single Responsibility Principle. We separate them because We can mock repositories for testing business logic separately.

2. - Code complexity increases: if Program, Subscriber and Notification all handle business logic and data access, the models will depend on each other too much. If we change one model, it could break the entire system.
- Dificulty debugging and maintainance: ff Notification logic is inside Subscriber, we might have duplicate logic across models. Fixing a bug means changing multiple places instead of just one Service layer.
- Performance issues: Without a Repository Layer, direct database access from multiple models could lead to redundant queries. No caching mechanism means slow performance.

3. - Quick API Testing → No need to write test scripts, just send requests.
- Automated Tests → Use Pre-request Scripts & Test Scripts to automate API testing.
- Collection Runner → Run multiple API tests in one click.
- Environment Variables → Easily switch between dev/staging/production APIs.
- Mock Servers → Simulate API responses without a real backend.

#### Reflection Publisher-3
1. Based on the code structure and the NotificationService.notify(...) method, the Push Model is used. The publisher (NotificationService) actively sends notifications to all subscribers when an event occurs. Subscribers do not request or pull data; they simply receive what is pushed to them. 

2. Advantages
- More control for subscribers: Subscribers decide when to fetch updates, avoiding unnecessary notifications. This can be useful if some subscribers need data less frequently.
- Less frequent updates reduce load: If subscribers only check for updates when necessary, it reduces the number of messages being sent.
- Handles large data more efficiently: Instead of pushing all data, subscribers can pull only what they need.

Disadvantages
- Delays in receiving updates: Subscribers must continuously check for updates, introducing delays (polling overhead). If a subscriber pulls data too late, it might miss important notifications.
- Increased complexity: Each subscriber needs logic to check for changes instead of simply reacting to pushes.The publisher needs to maintain a history of notifications so that subscribers can fetch the latest data when needed.
- Inefficient resource usage: If subscribers keep pulling data unnecessarily, it increases CPU and network load. Unlike the Push Model, which sends notifications only when needed, the Pull Model might introduce excessive API calls.

3. - Performance bottlenecks: If one subscriber takes a long time to process a notification, all other notifications are delayed.
- Application freezing: The entire server might freeze or slow down if notifications take too long. If notifications are sent synchronously (one by one), the system cannot handle multiple events efficiently.
- Scalability Issues: Without multi-threading, the system cannot handle multiple notifications at once.