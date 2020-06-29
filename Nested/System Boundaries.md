Boundaries in a software system can be difficul to categorize. I would argue that they fall into 2 main kinds though:

1) Inter-process
2) Intra-process

The process boundary is almost always a hard system boundary. An example of this is in the Client-Server architecture- this architecture places a boundary between a frontend and backend, which has enormous implications. Each piece can be written in separate languages, deployed independently, tested independently, etc. A "system" can be reductively viewed as a set of communicating processes. A database is a separate process. External services, such as S3, are separate processes. 

Changes that affect this boundary are almost always expensive. For example, changing the response shape of a server has a number of implications:

* Do we know which clients consume the keys that wre moved / renamed?
* Can we upgrade the cleint at the same time as the server?
* Did the client have tests stubbed / mocked that depended on that response shape?

