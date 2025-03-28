# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
```
In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why it is necessary for this case, and explain why we do not use Mutex<> instead?
```
Synchronization is necessary because multiple threads may need to read and write to the Vec of Notifications at the same time. Without proper synchronization, race conditions could occur, leading to data corruption or unexpected behavior. Since Rust enforces strict memory safety rules, using RwLock<> ensures that multiple readers can access the Vec concurrently while still allowing exclusive write access when needed. This improves performance compared to a simple locking mechanism that blocks all access during every operation.

A Mutex<> could also provide synchronization, but it is not as efficient in scenarios where multiple threads primarily need to read data rather than modify it. With a Mutex<>, even read operations require acquiring the lock, meaning only one thread can access the data at a time. In contrast, RwLock<> allows multiple readers simultaneously but ensures that only one writer can modify the data at any given moment. This makes RwLock<> the better choice when the application involves frequent reads and occasional writes, as is the case with our notification system.

```
In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static” variable. Compared to Java where we can mutate the content of a static variable via a static function, why did not Rust allow us to do so?
```
Rust does not allow direct mutation of static variables because it prioritizes thread safety and memory safety. In Java, a static variable is shared across the entire application and can be modified freely within static functions, but this can lead to concurrency issues such as race conditions and inconsistent state when multiple threads access the variable simultaneously. Java developers must use explicit synchronization techniques like synchronized methods or volatile variables to prevent these issues.

In Rust, static variables have a 'static lifetime, meaning they persist for the entire duration of the program. However, they are immutable by default to prevent accidental data races. If a static variable needs to be modified, Rust requires it to be wrapped in a thread-safe structure like Mutex<>, RwLock<>, or DashMap to ensure safe concurrent access. This design forces developers to think explicitly about synchronization, reducing the risk of unsafe memory access. Using lazy_static! allows safe initialization of complex static variables while still enforcing Rust’s strict concurrency guarantees.

#### Reflection Subscriber-2
```
Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not, explain why you did not do so. If yes, explain things that you have learned from those other parts of code.
```
Yes, I explored src/lib.rs outside of the tutorial steps because I wanted to understand how the project was structured beyond just following instructions. Looking into lib.rs helped me see how different modules were organized and how the main components interacted. I noticed that lib.rs serves as the entry point for shared functionality, re-exporting various modules so they can be used more easily throughout the project. This was particularly useful for understanding how the notification system was structured, as it clarified how different parts of the codebase were connected. It also gave me insight into how Rust handles modularization and how dependencies between different files are managed efficiently.

One of the things that stood out to me was how the Observer pattern was implemented across multiple modules. By reading through lib.rs, I saw how different structs and traits were defined and used, which deepened my understanding of how Rust enforces ownership and borrowing rules while maintaining clean abstractions. Additionally, I discovered how external crates were integrated into the project, such as lazy_static and DashMap, which provided thread-safe data structures for handling subscriptions and notifications. Exploring this file made me appreciate the importance of organizing Rust projects properly and how a well-structured lib.rs file can make a codebase easier to navigate and maintain.

```
Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?
```
The Observer pattern makes it easy to plug in more subscribers because each Subscriber is decoupled from the Publisher and only needs to register itself to receive updates. In this case, adding multiple instances of Receiver does not require modifying the existing notification system—new subscribers can be registered dynamically, and they will automatically start receiving updates. This flexibility ensures that the system remains scalable and that new subscriber types or instances can be integrated without altering the core logic of the publisher.

However, spawning multiple instances of the main application introduces additional complexity. Since each instance of the main app would likely have its own set of subscribers and programs, coordinating them efficiently could become challenging. If each main app instance manages a different set of subscribers independently, they may not interfere with each other. But if they need to share subscriber data or notification states, synchronization issues may arise. In that case, using a shared database or a distributed messaging system like Kafka or Redis Pub/Sub could help manage communication between multiple instances effectively.

```
Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).
```
Yes, I created my own tests and enhanced the documentation in my Postman collection, and it was incredibly useful for both my tutorial work and group project. Writing my own tests helped me verify that the notification system was working correctly under different conditions. I added test cases to check whether notifications were successfully sent to multiple subscribers, how the system handled errors, and whether it could process concurrent updates efficiently. By automating these tests, I was able to catch potential issues early and ensure that changes to the code didn’t break existing functionality. This also made debugging easier because I could quickly identify where failures were occurring.

Enhancing the Postman documentation also proved valuable, especially for collaboration. I added clear descriptions for each API endpoint, detailing the expected request parameters, response structures, and possible error codes. This made it much easier for my teammates to understand how to interact with the notification system without constantly referring back to the code. Additionally, I used Postman’s automated test scripts to validate API responses, checking for expected status codes and JSON structures. These improvements streamlined our testing process and ensured that everyone in the group had a consistent and well-documented way to test and use the API, making development more efficient and reducing miscommunication.