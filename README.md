# Introduction
This project provides practical guidance for systems/software engineers and teaching material for a graduate level course regarding non-functional requirements (NFRs). The following topics are covered:

- The classification of requirements and an explanation why NFRs are so important.
- What is Planguage and what are its benefits.
- Tool support in managing requirements including NFRs.
- Patterns and templates for implementing NFRs in a software system.
- The usage of [slidev](https://sli.dev/) to produce presentations in developer friendly manner and how to publish it as a static website via [GitHub Pages](https://pages.github.com/). This presentation is available [here](https://evarga.github.io/nfrs-guide/) and contains information about how NFRs drive software evolution and examples of properly specified NFRs in Planguage. **Make sure to watch after reading this README!**

The term non-functional may resonate as something non-important or related to things outside the core value stream. This is a huge blunder, as NFRs shape the architecture, and they are vital for expanding market share as well as ensuring customer retention. Some authors even refrain to use this term and many process frameworks call them supplementary or supporting requirements. We will stick to the term NFR in this project.

In software development _validation_ and _verification_ are two complementary viewpoints on all activities that are driven by requirements:
- Validation encompasses all activities for ensuring that we are building the right thing. This is the phase when requirements (both functional and NFRs) are rigorously examined for any conflicts and assurances collected that they meet customer's needs.
- Verification is a set of activities for checking whether we are building the product right. This entails establishing quality gates to avoid bugs including those associated with departing from requirements, missing them, or simply wrongly implementing the system. For example, the company could improperly design and construct the product, so the system turns out to be too slow to meet performance aspects. This can happen even if all performance related NFRs were impeccably specified.

## Prerequisites
Before we plunge into the realm of NFRs it is beneficial to give some guidance how requirements differ from user stories and what is the difference between user requirements and system/software requirements. Below is a set of curated references that should be studied before proceeding further:
- The article [Business, User, and System Requirements](https://enfocussolutions.com/business-user-and-system-requirements) explains the three major categories of requirements. Software requirements are defined in the context of system requirements, as the latter encompasses a whole system where software is only part of it.
- The blog [User Stories vs. Requirements](https://www.aha.io/blog/user-stories-vs-product-requirements) nicely recaps how user stories are not the same as requirements. The former may be regarded as a template/placeholder for future requirements. User stories organically transform into product requirements as developers start working on them in sprints.
- The article [Use cases vs user stories in Agile development](https://www.boost.co.nz/blog/2012/01/use-cases-or-user-stories) elaborates about another topic that frequently confuses people. User stories gradually incorporate missing details via attached acceptance criteria while use cases are better to capture behavioral requirements in a more detail. They are especially useful where formality matters, as describing interaction among the product and actors motivates stakeholders to discover alternate flows (edge cases) earlier. Sometimes handling these produces a bigger impact on the overall architecture than the main scenario. Furthermore, use cases may be visualized through UML use case and/or user journey diagrams where one can clearly emphasize the scope of a product and model actors.
- Finally, the last missing piece is illuminated in this post [The definition of done and acceptance criteria: What’s the difference?](https://www.boost.co.nz/blog/2019/04/difference-definition-done-acceptance-criteria), where the dissimilarities between *acceptance criteria* and *definition of done* are exemplified. The latter is something that is left to squads to specify, although there is a need to take a broader perspective including business criticality of applications. All work performed by squads impacting any component inside a mission-critical application must happen under more stringent definition of done compared to work done on a non-mission-critical application. The expense of fixing bugs rises exponentially as time passes by and requirements related bugs are hardest to remedy, as depicted in the [Defect Cost Increase](https://www.construx.com/resources/software-developments-defect-cost-increase-poster/) poster.

# Classification of NFRs
There are two broad categories of NFRs:
- **Product** - usual understanding of NFRs related to a solution. 
- **Process** - kind of meta NFRs pertaining to a development process.

## Product Oriented NFRs
Product related NFRs are encompassed in most well-established industry processes (like in [SAFe](https://scaledagileframework.com/nonfunctional-requirements)). We can differentiate two groups of NFRs that characterize quality:
- **External** - usability, performance, reliability, availability, security, etc. 
- **Internal** - scalability, portability, supportability, testability, etc.

The external attributes are usually perceivable by an end user, while the internal are enablers for a business to flourish. Both classes are equally important. If the system is not maintainable, then improvements, expansions, adaptations are all painful activities. Here are some examples of external NFRs (we will later dig deeper into how to express NFRs in a more precise manner):
- Performance: _Response time should be less than 1 second to display or refresh a webpage with order items_.
It is important to recognize that nobody should claim that this is purely a UI requirement. In order for a user to promptly see open deals all associated back-end services must run fast enough.
- Availability: _99.99% uptime during peak business hours (depends on a customer's region)_.
- Usability: _Frequent actions must be easily accessible from a UI via customizable keyboard shortcuts and toolbar_.
Usability tests are very hard to be fully automated. This is the territory where manual testing is still prevalent. It is not an uncommon practice to leave all usability concerns to an external source by mandating that specific UX guidelines should be followed[^1].

### Minimally Viable Set of Quality Attributes
There is a common set of important quality attributes abbreviated as FURPS+ (Functionality, Usability, Reliability, Performance, and Supportability). Functionality is about what the system should do, while NFRs tell how well it must perform the work. Interestingly enough, an architecture of a software system is profoundly responsible to address NFRs. If we wouldn't care about them, then functionality could be delivered in any manner, most would be useless. Excellent sources of information about FURPS+ is the [OpenUP](https://www.utm.mx/~caff/doc/OpenUPWeb/) framework[^2] and [this](https://www.marcinziemek.com/blog/content/articles/8/article_en.html) short blog.

#### Security as Technical Functionality
One of the reasons that functionality (denoted as F) in the FURPS+ quality model encompasses security is that security aspects of an application are directly perceivable by users of a system and often explicitly demanded as such. For example, account registration and management, login/logoff possibility, handling permissions (authorization), encryption of data, etc. are all things that are specified alongside other features. On the other hand, it is rarely the case that users would specify how documentation or supporting help system should be crafted, although if they turn out to be cumbersome, then users would rate usability lower.

A huge misconception in system development, which is attempted to be remedied by the FURSP+ model, is that security could be incorporated as an afterthought into a software. Security cannot be tuned like performance or improved via refactoring activities, that works so well to boost maintainability. Security concerns and requirements must be taken into account from the very beginning and materialize in form of core features of.a system. Try the [Secure Code Game](https://github.com/skills/secure-code-game) challenges to get a better understanding of how security is interwoven with other aspects of software development.

### Taxonomy of Product Related NFRs
NFRs as other requirements follow the same life-cycle model as functional requirements; it consists of requirement development (elicit, specify/quantify, analyze, and validate) and management (prioritize, estimate, schedule, and track) activities. Nonetheless, the first step is to establish an agreed taxonomy of NFRs. There is an industry standard quality model called [ISO/IEC 25010](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010) that defines and classifies NFRs. Having a common taxonomy avoids confusion about terminology. A good source that explains the most frequently used NFRs, how to design a system to satisfy them, as well as introduces life-cycle management aspects is the book [Software Architecture in Practice (SEI Series in Software Engineering) 4th Edition](https://www.amazon.com/Software-Architecture-Practice-SEI-Engineering-dp-0136886094/dp/0136886094/). For the sake of completeness, here is one possible hierarchical list of product related NFRs (the importance for deep structure will be demonstrated later):
- Usability 
  - Efficiency 
  - Learnability 
  - Productivity 
  - Operability 
  - Issue handling
- Reliability 
  - Robustness 
  - Recoverability 
  - Availability 
  - Fault-tolerance 
  - Observability
- Performance 
  - Scalability 
  - Capacity 
  - Throughout 
  - Latency
- Supportability 
  - Serviceability 
  - Portability 
  - Interoperability 
  - Compatibility
- Security 
  - Confidentiality 
  - Integrity 
  - Non-repudiation 
  - Authenticity 
  - Accountability
- Maintainability 
  - Testability 
  - Modularity 
  - Readability 
  - Reusability

The set of NFRs must be optimized for backlog items. This is usually a concerted effort of all stakeholders, where some NFRs are mostly dictated by business people while the others are under the jurisdiction of architects. The optimization is needed for the following reasons:
- It makes no sense to mention all possible NFRs for each backlog item. For example, if there is no need to develop a product that will run without any change in multiple cloud environments (AWS, Google, Microsoft Azure, etc.), then this type of portability should be omitted.
- It is not possible to satisfy all NFRs with utmost level. There are situations when NFRs conflict with each other. For example, increasing security would thwart usability, as any additional security measure is a burden for a user. Therefore, prioritization of NFRs is an unavoidable prerequisite.
- NFRs (at least, those that are regarded as architecturally significant requirements) shape the architecture of the system.  It is not feasible to expect that any combination of NFRs would be deliverable by the current architecture. This is where architects must actively negotiate with other stakeholders the NFRs and come to a mutually acceptable compromise. On rare occasions, maybe there will be a need to reengineer the system, but this must be aligned with a corporate strategy.

## Process Oriented NFRs
Unambiguously specifying NFRs for a product is inevitably a huge step toward higher quality. But it is equally relevant how people write down their initiatives, requirements, user stories, definitions of done, etc. The set of attributes that cover these aspects are denoted as process oriented NFRs. They are not directly related to the product, but they are crucial for the success of the project. These NFRs are usually not explicitly stated in the requirements specification, but they are implicitly present in the process framework that is used. OpenUP has a superb checklist[^3] about qualities of good requirements. As NFRs are also requirements, they must be traceable[^4], too.

User stories may be regarded as placeholders for requirements, and they should be written down with great care. The company named Boost uses the acronym INVEST (Independent, Negotiable, Valuable to user or business, Estimable, Small, and Testable) as a collection of quality characteristics for user stories. The booklet [User Story Examples](https://www.boost.co.nz/blog/2019/07/user-story-examples) enumerates various bad, good, and ugly examples of user stories in the light of INVEST. There is also an article [Definition of done examples and tips](https://www.boost.co.nz/blog/2019/05/definition-of-done-examples-and-tips) for examples pertaining to the notion definition of done. Finally, the [acceptance criteria checklist](https://www.boost.co.nz/blog/2010/09/acceptance-criteria) deals with properties of acceptance criteria. All of these artifacts help to ensure that installed processes are followed in controlled and repeatable manner.

All in all, process oriented NFRs are about how well a software development process is carried out. For example, traceability is a process NFR that ensures that all requirements are linked to each other. A requirement that a user story must obey INVEST is another example of a process NFR.

# Specification of NFRs
Testability of requirements (both functional and non-functional) entails writing them down in quantifiable manner. Otherwise, there is no way to know whether something is achieved or not. The biggest difference between functional and non-functional requirements is that a well-written functional requirement is relatively straightforward to test. On the other hand, NFRs require observation, inspection, and analysis for verification besides being adequately described.

Observability of the system is an internal attribute (like, testability, maintainability, etc.) not directly perceivable by a customer, although very important for the success of a product. NFRs usually require advanced monitoring of the production system. When NFRs are correctly specified, then DevOps engineers may directly use them to set alarms in monitoring tools. QA personnel can easily check whether they are met before placing the system into the hands of a customer. Using monitoring tools is mandatory, as discrepancies between promised and actual values must be promptly detected, alarmed and rectified. Many metrics could be collected whilst observing a system. Hence, some standardization of metrics[^6] may help a lot.

## Planguage
Let us go back to our earlier example of a performance related requirement, quoted here for the sake of completeness:
> _Response time should be less than 1 second to display or refresh a webpage with order items_.

A QA personnel would have a hard time testing this statement. What should happen if the timing was 1.1 seconds? It is clearly more than 1 second, but should the test fail? What about 1.9 second? This is the reason why we need intervals (ranges) of values and more formality in expressing NFRs. Such formalism is embodied in the Plangauge.

Planguage is Tom Gilb’s formal specification language for NFRs and is completely elaborated in his free book [Competitive Engineering](https://www.gilb.com/competitive-engineering) (be warned it is a tough reading even for professionals). Planguage uses keywords to quickly capture the relevant parts of an NFR without requiring long rhetoric. Furthermore, every keyword has a well-defined meaning which brings order into the whole process. The basic set of these is enrolled in the table below.

| Keyword    | Meaning                                                                           |
|------------|-----------------------------------------------------------------------------------|
| `Tag`      | Structured name of the NFR                                                        |
| `Gist`     | Brief description of the NFR                                                      |
| `Ambition` | Short briefing about the rationale behind the NFR                                 |
| `Scale`    | Unit of measure with context                                                      |
| `Meter`    | Measurement method                                                                |
| `Baseline` | The current level relative to the NFR in question                                 |
| `Fail`     | Point where the QA test fails                                                     |
| `Goal`[^5] | Point where we fully approve the test in a sense of having achieved our objective |
| `Stretch`  | Limit above which [ROI](https://www.investopedia.com/articles/basics/10/guide-to-calculating-roi.asp) is negligible                                           |

#### Why Structured Name?
Most NFRs are not atomic, but rather a collection of smaller NFRs. In the above-mentioned book _Competitive Engineering_ there is an example of breaking down Serviceability into Enhancement, Installation, Fashion Changes, Reconfiguration and Repair. For example, the structured name `Serviceability.Repair` talks about a particular perspective of being serviceable. The time and easy of repairing of a system is a crucial aspect of serviceability, but it is not the only one. The structured name helps to understand the context of the NFR. On the other hand, mentioning only the top level category is meaningless, since repair is not the same as installation or reconfiguration, although all of them are part of serviceability.

# Allocation of NFRs
Much like functional requirements are distributed according to [GRASP](https://www.pearsonhighered.com/assets/samplechapter/0/1/3/0/0130925691.pdf) patterns, NFRs must be allocated to software components. To make this statement more concrete, let us consider a simple example related to performance called _performance budget_. Suppose that a business comes up with a performance improvement regarding the response time for registering a new user. This is a quite high-level requirement that talks about the whole system. Giving this number directly to developers is pointless. This is where an architect must distribute the top-level performance figure onto individual components participating in the user registration use case. In this way, each squad would receive their allowed performance budget in this business transaction. Furthermore, not all components may need to deliver top performance all the time. Everything depends on a particular use case. One way to visualize the allocation of performance budget per component is via the fishbone diagram. The branches would be contributions of components toward the final performance figure.

Allocation also works in "reverse" direction, when architects must reason about emergent properties of a system. In a complex distributed system, especially in service-oriented architectures, due to interrelationships of many moving parts over an asynchronous network (like, the Internet), some system properties simply emerge from these interactions. Long spanning business transactions involve coordinated effort of many components, starting from a front-end website and ending with a commit into a database. Taking out any component from this chain and analyzing it in isolation cannot tell the whole story. For example, overall reliability and security of a system often depend upon the weakest link, so an architect must take into account the full set of components in each sequence of actions to evaluate whether these NFRs can be satisfied.

# Conclusion
This paper introduced NFRs from two perspectives: product and process. The former is about the quality of a system, while the latter is about the quality of the development process. The importance of NFRs is underscored by the fact that they shape the architecture of a system. The paper presented the Planguage as a formal specification language for NFRs. The paper also discussed the allocation of NFRs to software components.


[^1]: For exmaple, the set of conventions that constitute a good design for Android devices, [learn more](https://developer.android.com/design).
[^2]: Navigate to OpenUP Work Products > Requirements > Supporting Requirements Specification > 🔑 Supporting Requirements.
[^3]: Navigate to OpenUP Work Products > Requirements > Supporting Requirements Specification > Qualities of Good Requirements.
[^4]: Navigate to OpenUP Disciplines > Requirements > Traceability.
[^5]: Planguage differentiates between Goal (performance) and Budget (resource). We will not make such a distinction in this project. The term Goal is used in the sense of a target that must be achieved.
[^6]: [Application Performance Index (Apdex)](https://www.apdex.org) is an open standard developed by an alliance of companies. It defines a standard method for reporting and comparing the performance of software applications in computing. Its purpose is to convert measurements into insights about user satisfaction, by specifying a uniform way to analyze and report on the degree to which measured performance meets user expectations.