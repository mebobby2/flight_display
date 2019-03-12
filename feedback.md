# Feedback from the Xero team

## Analysis
* Picked up that airlines probably won't have duplicate flight numbers. Good that he's thinking about things outside of the solution.
* Picked up that a flight can only leave once per day, which is good.

## Data modelling
* Describes his decisions well and thinks about how data will be searched and queried.
* Seems to model the business objectives well.

## Solution design
* Workable solution. Mostly scalable and he seems to know some of the issues which shows that he considers technical debt.
* Manages the private/public problem ok. Shows using a VPN.
* Doesn't talk about how he arrived at the technology choices he's chosen.
* Speaks about agile, which is great.
* Good - Spoke about CI/CD pipeline, etc.
* If pushed for an estimate, he would give one of 8 months, which is ok.
* No talk of testing at all. Spoke about devops, design effort, development effort, etc.
* No talk of team size or skills required, but that's understandable based on his explanation

# Preparations for next round

1. Talk about technology choices
2. Think about testing
3. Come up with team size and skills
4. Revised the 8 months estimate

## Technology choices
### HAProxy
#### What Is a Reverse Proxy Server?
A proxy server is a go‑between or intermediary server that forwards requests for content from multiple clients to different servers across the Internet.

Common uses for a reverse proxy server include:
1. **Load balancing** – A reverse proxy server can act as a 'traffic cop', sitting in front of your backend servers and distributing client requests across a group of servers in a manner that maximizes speed and capacity utilization while ensuring no one server is overloaded, which can degrade performance. If a server goes down, the load balancer redirects traffic to the remaining online servers.
2. **Web acceleration** – Reverse proxies can compress inbound and outbound data, as well as cache commonly requested content, both of which speed up the flow of traffic between clients and servers. They can also perform additional tasks such as SSL encryption to take load off of your web servers, thereby boosting their performance.
3. **Security and anonymity** – By intercepting requests headed for your backend servers, a reverse proxy server protects their identities and acts as an additional defense against security attacks. It also ensures that multiple servers can be accessed from a single record locator or URL regardless of the structure of your local area network.

There are 3 popular load balancing techniques:
1. Round-Robin
2. IP Hash
3. Least Connections

#### Why HAProxy
The other popular open-source option is Nginx. Nginx is capable of doing much more than load balancing it is a much more versatile solution. It's useful if you require a load balancer as well as a web server with advanced caching ability, serving static content, and more.

HAProxy is a single-purpose solution in that it only offers load-balancing capabilities, it is much more focused on that one aspect compared to Nginx.

In the end, decided to go with HAProxy because it's designed from the ground up to be load balancer. Nginx is a web server that can be used as a load balancer. Since we only require this piece of the architecture to be balance load, we should use a software that is soley designed to handle that.

### JWT
OAuth 2.0 defines a protocol, i.e. specifies how tokens are transferred, JWT defines a token format.

Auth 2.0 may use a JWT as a token format.

We opt not to use OAuth because of the complexity involved. Also, for mobile apps, you would have to use the Password Grant Type Flow. This flow exchanges an access token from a username and password. We use this grant type because the mobile apps are trusted. Hence, why go through all the troubles of setting up oauth when we just need simple bearer tokens?

#### Why Use JSON Web Tokens (JWT)?
Let's talk about the benefits of JSON Web Token (JWT) when compared to Simple Web Token (SWT) and Security Assertion Markup Language (SAML) tokens.

* More compact - JSON is less verbose than XML, so when it is encoded, a JWT is smaller than a SAML token. This makes JWT a good choice to be passed in HTML and HTTP environments.
* More secure - JWT can use a public/private key pair in the form of an X.509 certificate for signing. SWT can only be symmetrically signed by a shared secret using the HMAC algorithm. And while SAML tokens can use public/private key pairs like JWT, signing XML with XML Digital Signature without introducing obscure security holes is very difficult when compared to the simplicity of signing JSON.
* More common - JSON parsers are common in most programming languages because they map directly to objects. Conversely, XML doesn't have a natural document-to-object mapping. This makes it easier to work with JWT than SAML assertions.
* Easier to process - JWT is used at internet scale. This means that it is easier to process on user's devices, especially mobile.

### NodeJS
The solution involves accessing the API from mobile clients. To target more mobile platforms, we can use a cross-platform framework like React Native. React Native is ideal for our problem domain because our mobile apps have no need for CPU intensive or parallel processing tasks.

We want to keep the solution isomorphic and use Javascript everywhere. This keeps the barrier to entry low for developers and allows them to work on both mobile apps and api servers seamlessly. This means we should use NodeJS for our back end servers.

### MySQL
The hot data for our application is quite small. Most customers only want to see active flights and ignore flights in the past. Because of this, we do not anticipate our data size growing much. Hence, there is no need for NoSQL databases.

We can further optimize hot data loading by running a nightly job to archive past flights. This keeps the table sizes as small as possible to speed up database queries.

The advantage of using MySQL:
Widely used
* Mature technology
* Clear scaling paradigms (sharding, master/slave replication, master/master replication)
* Index lookups are very fast

Why MySQL over PostgreSQL? You can choose either of the two and it probably woudln't make a difference to our solution. We choose MySQL just because it seems like its the most popular of the two.

### ActiveMQ
Only famaliar with RabbitMQ and ActiveMQ. Decided to use ActiveMQ because its written and it's easier to find Java developers in case we needed to debug or fine tune the broker. RabbitMQ is written in Erlang, and we don't want that dependency because its harder to find Erlang developers.

## How I would run the project

This would answer the remaining preparation points:
1. Think about testing
2. Come up with team size and skills
3. Revised the 8 months estimate

### Master Story List
As the tech lead, I would work with the Business Analyst or Project Manager to break down the system into stories. We need to make sure the stories are sliced vertically instead of horizontally. We can also add in tech stories such as setting up CI, or tech spikes for certain parts of the system we are not clear on.

We will then do story estimation with the rest of the dev team. We highlight that this is only an estimate and the points could change during the project. We use a point based estimation instead of time based. We use planning poker to do estimation. At the stage, we have all the points for the master story last, however, we still do not know how long it will take us to complete the project because we don't have the team velocity yet.

The tech lead then works with the BA to priortise the stories. Priorities can be based on business or tech. For example, some tech stories needed to be completed before we can begin on the feature stories.

Some example stories for this project:
1. Sign up flow for Flight Status App
2. Loading a list of pending flights in Flight Status App
3. List Flights API (for big tracker board)
4. Set up DB
5. Set up message queue
6. Set up load balancer and app servers

### Team structure
A typical scrum team is 6-8 devs. We request for a team of 6 devs first. And grow the team holistically as the work load increases. We also request for a UX and a QA.

Skills wise of the devs, we just need experience in working with:
* Javascript/Typescript
* React Native
* ExpressJS
* MySQL
* Certain build tools like BambooCI
* Devops knowledge

### Iteration 0
During this iteration, the UX can work on wireframes and screens/flows for the application while the dev team can work on tech tasks such as:
* Setting up walking skeleton applications
* Setting up CI to deploy applications into QA/UAT/PROD
* Performing any tech spikes

The BA can work on writing user stories.

### Development
* We work in pairs.
* We do TDD.
* Each developer is full stack.
* We do story kick-offs.
* We do desk checks
* We do daily standups
* We do sign in/out after standup
* Story acceptance criterias should include any non-functional requirements
* Devs write unit tests and integration tests as part of TDD
* We do trunk-based development
* We make small frequent code commits
* All commits trigger testing and building steps on the CI, which will deploy to the QA environment automatically
* Pushes to UAT/PROD are manual
* We do sprints of 2 weeks
* At the end of each sprint, we do PO acceptance and demos
* We do retros after every sprint

During desk checks, the story should be demo-ed in either UAT or QA, depending on our definition of 'done'.

Once the story is desk-checked, the QA tests the stories manually and writes any E2E tests. The QA should keep in mind that E2E tests should only test the critical flow.

### After a few iterations
Only after a few iterations, where we are sure our team velocity has stablelised, only then can we look at all the points in the backlog and estimate how long the project will take.

### So, did we answer the questions?
1. Think about testing - yes, we do testing as part of TDD and the QA does manual and E2E testing
2. Come up with team size and skills - we start off with the ideal scrum team size of 6 developers, while requesting developers with specific technologies the project uses
3. Revised the 8 months estimate - we do not have a hard estimate because we do not have the master backlog. But I have outlined how and when this estimate can be made.
