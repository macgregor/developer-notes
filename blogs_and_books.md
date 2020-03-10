
# Reading List
[Current] 1. The Pragmatic Programmer by David Thomas & Andrew Hunt
[Ordered] 2. Domain-Driven Design by Eric Evans
[] 3. Patterns of Enterprise Application Architecture by Martin Fowler
[] 4. Clean Code by Robert C. Martin
[] 5. Refactoring by Martin Fowler
[] 6. Working Effectively with Legacy Code by Michael Feathers
[] 7. Cracking the Coding Interview by Gayle Laakmann McDowell

# Research List
* DBC, Design By Contract
  * Object-Oriented Software Construction by Betrand Meyer
  * programming languages:
    * Eiffel
    * Clojure
    * Elixer (pseudo DBC, guard clauses)


# Blogs
* [Practical Dev](https://dev.to/?utm_source=website&utm_medium=blog&utm_campaign=bestengineeringblogs2017&utm_content=12112017)
* [Coding Horror](https://blog.codinghorror.com/)
* [Martin Fowler](https://martinfowler.com/)

# Books

## Most recommended Programming Books of All Time
[Most recommended Programming Books of All Time](https://www.reddit.com/r/programming/comments/f9ut5f/the_most_recommended_programming_books_of_alltime/)
* Feb 2020, compiled from 208 lists from Google search results for queries like "Best programming books"

1. The Pragmatic Programmer by David Thomas & Andrew Hunt (67% recommended)
2. Clean Code by Robert C. Martin (66% recommended)
3. Code Complete by Steve McConnell (42% recommended)
4. Refactoring by Martin Fowler (35% recommended)
5. Head First Design Patterns by Eric Freeman / Bert Bates / Kathy Sierra / Elisabeth Robson (29.4% recommended)
6. The Mythical Man-Month by Frederick P. Brooks Jr (27.9% recommended)
7. The Clean Coder by Robert Martin (27.9% recommended)
8. Working Effectively with Legacy Code by Michael Feathers (26.4% recommended)
9. Design Patterns by by Erich Gamma / Richard Helm / Ralph Johnson / John Vlissides (25% recommended)
10. Cracking the Coding Interview by Gayle Laakmann McDowell (22% recommended)
11. Soft Skills by John Sonmez (22% recommended)
12. Don’t Make Me Think by Steve Krug (19.1% recommended)
13. Code by Charles Petzold (19.1% recommended)
14. Introduction to Algorithms by Thomas H. Cormen / Charles E. Leiserson / Ronald L. Rivest / Clifford Stein (17.6% recommended)
15. Peopleware by Tom DeMarco & Tim Lister (17.6% recommended)
16. Programming Pearls by Jon Bentley (16.1% recommended)
17. Patterns of Enterprise Application Architecture by Martin Fowler (14.7% recommended)
18. Structure and Interpretation of Computer Programs by Harold Abelson / Gerald Jay Sussman / Julie Sussman (13.2% recommended)
19. The Art of Computer Programming by Donald E. Knuth(10.2% recommended)
20. Domain-Driven Design by Eric Evans (10.2% recommended)
21. Coders at Work by Peter Seibel (10.2% recommended)
22. Rapid Development by Steve McConnell (8.8% recommended)
23. The Self-Taught Programmer by Cory Althoff (8.8% recommended)
24. Algorithms by Robert Sedgewick & Kevin Wayne (8.8% recommended)
25. Continuous Delivery by Jez Humble & David Farley (8.8% recommended)

# Architecture

## Software Architecture Guide - Martin Fowler
Broad overview of Martin Fowler's thought son application and enterprise architecture with links to further materials on specific topics.

https://martinfowler.com/architecture/
* good architecture is something that supports its own evolution, and is deeply intertwined with programming
* *architecture*: "Architecture is about the important stuff. Whatever that is" - Ralph Johnson
  1. the shared understanding that the expert developers have of the system design
  2. “the design decisions that need to be made early in a project” or the decisions you wish you could get right early in a project
* the heart of thinking architecturally about software is to decide what is important, (i.e. what is architectural), and then expend energy on keeping those architectural elements in good condition
* a poor architecture is a major contributor to the growth of cruft - elements of the software that impede the ability of developers to understand the software
* High internal quality leads to faster delivery of new features, because there is less cruft to get in the way (attention to internal quality pays off in weeks not months)
* *Application Architecture* - architecture within some form of notional application boundary:
  * [Application Boundary](https://martinfowler.com/bliki/ApplicationBoundary.html)
  * [microservices](https://martinfowler.com/microservices/)
  * serverless architectures
  * micro frontends
  * gui architectures
  * [Presentation Domain Data Layering](https://martinfowler.com/bliki/PresentationDomainDataLayering.html)
* *Enterprise Architecture* - architecture across a large enterprise, require coordination across teams with many codebases, that have developed in isolation from each other, with funding and users that operate independently of each other:
  * Enterprise Architects Join the Team
  * The Role of an Enterprise Architect in a Lean Enterprise
  * [Products Over Projects](https://martinfowler.com/articles/products-over-projects.html)
  * The Architect Elevator — Visiting the upper floors
  * [Enterprise Integration Using REST](https://martinfowler.com/articles/enterpriseREST.html)

## Microservices Guide - Martin Fowler

Breaks down what microservices are and the challenges to designing, building and maintaining them. Broad overview with links to specific topics.

https://martinfowler.com/microservices/
* common characteristics of [microservice architectures](https://martinfowler.com/articles/microservices.html):
  * Componentization via Services
  * Organized around Business Capabilities
  * Products not Projects
  * Smart endpoints and dumb pipes
  * Decentralized Governance
  * Decentralized Data Management
  * Infrastructure Automation
  * Design for failure
  * Evolutionary Design
* when to use [microservices](https://martinfowler.com/articles/microservice-trade-offs.html)
  * strengths:
    * strong module boundaries
    * independent deployment
    * technology diversity
  * weaknesses:
    * distribution
    * eventual consistency
    * operational complexity
  * [microservices premium](https://martinfowler.com/bliki/MicroservicePremium.html)
  * [monolith first](https://martinfowler.com/bliki/MonolithFirst.html) vs [dont start with a monolith](https://martinfowler.com/articles/dont-start-monolith.html)
  * [microservices prerequisites](https://martinfowler.com/bliki/MicroservicePrerequisites.html)
  * [microservices and the first law of distributed objects](https://martinfowler.com/articles/distributed-objects-microservices.html)
* building microservices
  * [testing strategies in a microservice architecture](https://martinfowler.com/articles/microservice-testing)
  * [how to break a monolith into microservices](https://martinfowler.com/articles/break-monolith-into-microservices.html)
  * micro frontends
  * [how to extract a data-rich service from a monolith](https://martinfowler.com/articles/extract-data-rich-service.html)
  * [infrastructure as code](https://martinfowler.com/bliki/InfrastructureAsCode.html)
  * [devops culture](https://martinfowler.com/bliki/DevOpsCulture.html)
  * [circuit breaker](https://martinfowler.com/bliki/CircuitBreaker.html)

# Productivity

## Outcome Over Output - Martin Fowler
Measuring a team's productivity using outcome based measurements/goals (e.g. fewer support calls) rather than output based measurements/goals (e.g. stories/points completed)

https://martinfowler.com/bliki/OutcomeOverOutput.html
* *output* - how many new features they produced in the last quarter, or a cross-functional measure such as a reduction in page load time
* *outcome* - increased sales revenue, or reduced number of support calls for the product
*  no clear way to measure the productivity of a software team (further complicated by whether we base effectiveness on output or outcome)
* If a team delivers lots of functionality (*output*) that functionality doesn't matter if it doesn't help the user improve their activity
* there is certainly a skill to picking the right outcomes to observe.
  * [Seiden](https://www.amazon.com/gp/product/B07QJ1Y8Y5?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=B07QJ1Y8Y5) says that an outcome should be a change in behavior of a user, employee, or customer that drives a good thing for the organization
  * [Highsmith, Luu, and Robinson](https://www.amazon.com/gp/product/0135263077?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0135263077) advise that outcomes about customer value (reliability of a dishwasher) should be given more weight than outcomes about business value (warranty repair costs).
* A consequential concern about using *outcome* observations is that it's harder to *apportion* them to a software development team. Consider a customer team that uses software to help them track the quality of goods in their supply chain. If we assess them by how many rejects there are by the final consumer, how much of that is due to the software, how much due the quality control procedures developed by quality analysts, and how much due to a separate initiative to improve the quality of raw materials?
* "you get what you measure" vs "you get what you try to measure" - If you focus appraisal of success on output, then everyone is thinking about how to increase the output. Instead, thinking about outcomes and how to improve them is worth more than any effort to compare teams' proficiency in producing the wrong things.
