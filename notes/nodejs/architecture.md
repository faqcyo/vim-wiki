[Back to NodeJS Notes](index)

# Architecture

The three layer approach:

First Layer: Controller
Second Layer: Service
Third Layer: Model

[Clean Architecture in Node.js](/notes/conferences/3)
[Node Clean Architecture Deep Dive](/notes/conferences/4)

Topics to take into account:

Separation Of Concerns
Single Resposibility Principle


Dependencies on Clean Architecture

x -> y means that x depends on y. That is, inside the x file there is a require('y').

Controllers -> Services
Services -> Repositories and Models
Repositories -> Models
Routes -> Controllers
Middlewares used by Routes and Controllers

# Example Project Structure

* src
  - entities
  - models
  - repositories
  - services
  - controllers
  - routes
  - middlewares
  - utils


# Difference between controller and service

The controller handles the User request and tells the service to process the data, so that the controller can then return to the user.
The controller doesn't do any work with the data, it just passes it down to the service.
The service contains the business logic to manage this data and return it to the controller.
The service can communicate with the repository.

In essence, controllers handle the "how" of dealing with HTTP requests, while services handle the "what" and "why" of your business logic


