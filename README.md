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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. **Why is `RwLock<>` necessary instead of `Mutex<>`?**  

   In our receiver application, we use `RwLock<Vec<Notification>>` to synchronize access to the notification list. The key advantage of `RwLock<>` is that it allows multiple threads to read simultaneously while ensuring only one thread can write at a time. This makes it ideal for a scenario where reading occurs more frequently than writing.  

   The reasons for choosing `RwLock<>` include:  
   - It allows multiple concurrent readers, improving efficiency in read-heavy situations.  
   - Readers are not blocked unless a write operation is in progress, reducing contention.  
   - It ensures thread safety by preventing race conditions.  

   On the other hand, `Mutex<>` is not as suitable because:  
   - It only permits one thread to access the data at any time, even for reading.  
   - Since reading is frequent, a `Mutex<>` would unnecessarily block multiple readers while a write operation is happening, decreasing performance.  

   Given our application's workload, `RwLock<>` provides better performance and scalability than `Mutex<>`, as it minimizes unnecessary locking during read operations.  

2. **Why doesn’t Rust allow mutation of static variables like Java?**  

   In Java, static variables can be modified directly within static methods. However, Rust enforces stricter rules to maintain thread safety and prevent data races. Rust does not allow direct mutation of static variables because:  
   - It ensures thread safety at compile time, reducing the risk of race conditions.  
   - Allowing `static mut` could lead to unpredictable behavior in multi-threaded environments.  
   - Unlike Java, which relies on a Garbage Collector (GC) for memory management, Rust enforces strict ownership and borrowing rules.  

   Instead of directly mutating static variables, Rust provides safer alternatives, such as:  
   - **Using `lazy_static!`**: This allows us to initialize and wrap global variables in synchronization primitives like `RwLock<>`, ensuring safe concurrent access.  
   - **Using `DashMap`**: Unlike a `Vec`, `DashMap` is optimized for concurrent reads and writes, making it a good alternative for shared mutable state.  

   In summary, Rust prioritizes memory safety and concurrency safety over convenience. Unlike Java, which permits direct mutation of static variables, Rust requires explicit synchronization mechanisms (`RwLock<>`, `Mutex<>`, `DashMap`, etc.) to ensure safe access to shared data.

#### Reflection Subscriber-2

1. **Have you explored files outside of the tutorial steps, such as `src/lib.rs`? If not, why? If yes, what did you learn from them?**  

   Yes, but only briefly. From my understanding, `src/lib.rs` acts as the core module of the project. This file includes essential elements like crate and module imports, the `AppConfig` structure, and error handling logic.  

2. **Now that you've completed the tutorial and tested your notification system with multiple `Receiver` instances, how does the Observer pattern simplify adding more subscribers? Additionally, what challenges arise when running multiple instances of the Main app?**  

   The Observer pattern makes adding new subscribers easy by allowing each instance of the Main app to manage its own set of subscribers efficiently. If multiple instances of the Main app need to broadcast changes to all subscribers, an inter-instance messaging system would be needed. However, adding subscribers across different app instances can still be handled smoothly by making HTTP requests to the appropriate API endpoints.  

3. **Have you written your own tests or improved documentation in your Postman collection? If so, how useful were these features for your work (whether for the tutorial or a group project)?**  

   Yes, and these features have been very useful. Writing tests helps ensure that the code functions correctly and prevents potential bugs. Enhancing documentation in Postman makes API usage clearer by outlining endpoints, required parameters, expected responses, and error handling details. Using Postman’s "Overview" tab is a great way to structure and present this information effectively.