# Client-server
The most common full-stack architecture is client-server. This is the highest level of the architecture, and it has the most impact. Within each process, different architectures can be chosen, but 

Fore this reason, refactoring away from a client server architecture would be very costly, because this assumption is everywhere in the code ([[Refactoring]])

The most typical client server architecture for an interactive application involves a client, server, as well as a database. There is typically an asynchronous job queue as well as a cache.

The client interacts with the server, and also manipulates data to be displayed for specific interactions. Here, concerns such as local caching, pagination, authentication, and state management are taken care of. This constitutes _application logic_, which is the the logic related to the interaction layer of the system.

The server accepts commands and returns queries. Queries can get complicated (involving multiple entities), and commands respect business rules. The purpose of the server is to have a trusted place where the environment is fully controlled. The server coordinates data access, authentication, authorization, etc. The server has some application logic, such as pagination and authentication, but it also houses domain / business logic. 

The main impact of a client-server architecture is that the [[Use Cases]] are now separated into two parts. You can't carry out a use case with just the client or the server, you need both. 

Thought: is it possible to build and test the concerns independently? i.e., pagination, authentication, domain logic. A use case will then just be the composition of these system concerns, i.e. each concern is a "shell command" and the use case is a "shell script."

# Visually
