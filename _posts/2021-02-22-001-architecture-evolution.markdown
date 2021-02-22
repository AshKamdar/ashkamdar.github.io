---
layout: post
title: From legacy to contemporary architectures
date: 2021-02-22 00:00:00 +0530
description: A journey into evolution of Enteprise IT architectures
comments: true
tags:
  [
    Legacy,
    Monolith,
    Microservices,
    Cloud,
    DevOps,
    Docker,
    Containers,
    Kubernetes,
    Opensource,
  ]
---

<table class="center">
  <tr>
    <td>
      <img src="{{ site.url }}{{ site.baseurl }}/assets/img/001/home.jpg">
      <span>Photo by <a href="https://unsplash.com/@danist07?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">DANIST</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>
    </td>
  </tr>
</table>

## Inception

If you have been in IT for long, you would have observed the following evolution of architectures in Enterprise IT.

1. Mainframes
2. Minicomputers
3. Client-Server architecture(2-tier)
4. Webserver-based architectures (3-tier....n-tier)

A <u>tier</u> is basically _logical separation of concern_. Along with evolution in tiers, the way code was written also kept changing.

Rather than producing a giant lump of code, it got more and more compartmentalized based on business units, teams, functions etc. This came to be known as **components** of the system.
So the system as a whole was the sum of its parts (components). All the components were tightly coupled with each other and created depenency chains in the system. The dependency were managed by using **Interfaces**.

However, these changes were happening only on <u>logical</u> side. Physically, all the components were lumped and deployed together. Also, all the components were working with a single centrally-managed database shared across the system. This is what we term today as <mark>Monolithic architecture</mark>.

The benefit was low latency communication across all components and a cohesive system that could be packaged and deployed at one go.

But it also brought with it some issues. As the system started to grow, it became more complex to make even a small change without undergoing rigorous testing to make sure nothing was broken.

Also, the system became expensive to run, as scaling it meant scaling the whole system even if only a few components were overloaded with processing and required to scale.

## Monolith to Microservices

So the next natural step in evolution was to make each of these components an individual system on its own, which can be developed, deployed, changed and hosted independently without worrying about its associated components.

In order to manage dependencies, the <u>Interfaces</u> were transformed into **API's**.  
These API's served as a contract between two dependent systems components and as long as the contract remain unchanged anything under it (the implementation) was free to change. If there were changes required in contract, the APIs could be versioned and that is how the systems development continued.

This was a revolution of sorts, as it allowed system components to break free from the bondage of the project and have a life of their own. They could be developed in different programming languages without worrying about compatibility issues. This is what is known today as <mark>Microservice architecture</mark>.

## Databases: Unity to Diversity

With the advent of breaking the tiers, the backend tier (database) also began to take advantage of this distributed architecture. Rather than having one single database for the whole project, now each system component could be designed to have databases of their own and there was no need to keep a central database ownership maintained by a single set of people.

Now databases were managed independently as part of each system component. There was no requirement to do a huge co-ordination exercise between all components of the system whenever structures in database needed to change.  
Also it was possible to choose depending on the scenario, there were choices available to use different RDBMS database technologies as well as take advantage innovations happening for storing and processing semi-structured and unstructured data (NoSQL, Blob storage etc.).

This was another paradigm change that meant each part of the system was free to choose optimum mix of technologies on the database side as well without taking consent from any central project team. They were responsible for lifecycle of the database along with their system components and ownership of database was also thus distributed among various system component owners, rather than keeping a team of DBA's engaged in management and maintenance of a shared database hosted on the same technology (like Oracle)

## Packaging Innovation

One more innovation that accelerated the trend of adopting microservice architecture in Enterprise IT was the advent of <mark>Docker</mark>.

Docker allowed each system component to be packaged with all its dependencies as a bundle. This bundle was called a **Container**.  
Another innovation that was mind blowing was that these containers could be hosted on different operating systems like Windows, Linux, MacOS etc. and they ran seamlessly on all of them. This was a paradigm shift in how things were packaged and how packages were deployed to run on servers.

Before this time, there were elaborate requirements of what would be needed on servers to host these system components. The list included a particular flavor of OS (Windows, Linux, Solaris etc.) with a particular version of OS, along with a specific versions of language runtimes, libraries, 3rd party packages etc.

It was practically like magic, to be able to run a packaged system component seamlessly on a <u>different OS altogether</u>, much less on a different version of same OS. This was made possible by **Docker**, as it packaged all the dependencies required for running the code in a container image and did not depend on their existence outside it.  
Docker also broke the tight integration between code and its hosting layer and provided abstractions that could be used for working with layers beneath it.

## Container Orchestrator

As more and more system components started getting hosted as containers, there was a need to manage, maintain and monitor all these containers at runtime. This gave birth to companies innovating on orchestrating containers and many technologies stated getting developed for this purpose like Apache Mesos, Docker Swarm, Kubernetes etc.

Out of this battle of container orchestrator, the clear winner now is <mark>Kubernetes</mark>.  
This came about by google having work with similar technology inhouse, called <u>Borg</u>, for 15 years. They brought all their knowledge out in community and this helped Kubernetes to become the mainstream technology for running containers.

## Rise of Opensource

There was a time when both **Software** and **Hardware** were created, owned and managed by big organizations called IT Vendors.  
These vendors were in the business of catering to demands of various enterprises by fulfilling their needs for different products and services in hardware and software.  
The vendors retained full control of their products and services and maintained large inhouse teams for development and maintenance of their offerings. They also charged huge licensing fees to enterprises who were interested in using their offerings.

With advances in software paradigm, the dependency on specific hardware started to reduce and this was the start of using commodity hardware for hosting Enterprise IT systems. Also trends like **virtualization** helped to elastically manage a pool of such hardware resources that could be expanded and contracted depending on the demand within an enterprise.

But software (and databases) was still in control of large IT enterprises. They owned all the rights to running their products and sometimes dictated terms that were not very friendly and flexible to the interest of the business enterprises.  
Also the enterprises were at risk because if any of the vendor went out of business, they were saddled with systems that were no longer supported by anyone and this would become a ticking timebomb.

<mark>Opensource</mark> came to the rescue to break this control and dominance of these few companies and provide long lasting solutions to above problems.

Opensource is nothing but a community of like-minded people coming together to develop a product or service that is <u>free</u> to be used by anyone. These people are called **Contributors** to an opensource project.  
Advances like **Git version control system** and **GitHub** made it possible for hundreds or even thousands of people located geographically apart, in different time zones, to come together and develop something that was the need of the hour.

First targets were database and soon there was a proliferation of different kinds of databases available to be used by enterprises <u>free of charge</u>.

Then came the turn of software’s that were tightly controlled by organizations.  
Opensource communities started developing an alternative version of such software’s and its adoption started gaining rapidly.

Because of large participation of experts world-wide in opensource, it is impossible for any organization to match the speed, quality and time-to-market of opensource projects. It also puts an end to huge licensing costs that business must pay, as well as breaks the monopoly and risks associated with particular vendors. They also get to influence the direction of the product and can enroll their own people in the development to provide feedback and features deemed useful for themselves and other enterprises.

This trend is now extending in all areas of business enterprises and there is a mad rush to adopt such opensource technologies as much as possible into their IT architectures.

## Legacy to Contemporary architectures

Today the systems that are prevalent in enterprise, that are based on the old architectures are termed as **Legacy** systems. Such systems are plagued with many problems like they are hard to change, costly to maintain, difficult to scale, run the risk of becoming obsolete, require specific type of manpower dedicated to managing each distinct system etc.

All this has led to enterprises showing a lot of interest in migrating these legacy systems to modern times. Hence there is a proliferation of projects that target such modernization initiatives and all these projects will adopt latest innovations mentioned above.

## Cloud computing: The great equalizer

The other game changers is birth of <mark>Cloud computing</mark>.  
Amazon figured out early in last decade that it could offer its free resources to be used by other enterprses on a pay-per-use basis. This was a win-win as Amazon could make money from their excess capacity while other enterprises would be able to use these services without big and upfront investment costs.

This enabled the complete stack of Compute, Storage, Networking, Security etc., to be offered as **Software-defined services**. Hence this gave rise to different models in Cloud computing like

1. Infrastructure-as-a-Service (IaaS)
2. Platform-as-a-Service (PaaS)
3. Software-as-a-Service (SaaS)

This allowed enterprises of all sizes (small, medium, large and behemoths) to be able to use the same type of resources and pay only based on the usage, essentially turning <u>Capex</u> to <u>Opex</u>.

This removed the barrier of large enterprises being able to maintain their lead by investing in expensive technologies that only they could afford to procure.

Also, this allowed every enterprise to start thinking about the costly data centers they possessed and there is a slow but sustainable trend of enterprises retiring their data centers and moving all their assets to cloud.

This helped accelerate the trend of migration to contemporary architectures, as now there are 100's of services offered by cloud provider to choose from, and enterprises can concoct the best mix of these services to achieve their desired goals.

## DevOps

Another area that has come out clearly and that has contributed to acceleration of deployment and release cycle is <mark>DevOps</mark>

Using the gamut of products and service available for **Continuous Integration (CI)** and **Continuous Delivery (CD)** from cloud provider or opensource, Enterprises can accelerate development of new features, shorten their release cycle and incorporate fail-fast and feedback loop mechanisms to mature their offerings quickly and offer it to their customers sooner and on a regular basis.

This helps to automate the manual release processes and reduce the release cycle times from <u>days/weeks</u> to <u>hours/minutes</u>.  
But this requires the IT systems to be able to leverage such technologies and be compatible with it in order to benefit from it.

## Future

Although it seems that pace of innovation has gone exponential in last decade, we are just scratching the surface in terms of what future will bring.

New technologies like Artificial Intelligence, Machine Learning, Robotics, Blockchain, IoT, Virtual reality, Speech recognition and many more, will get mature and will be ready to be used in practical scenarios.

But the only way you can benefit from using such innovations is if your systems are based on modern architectures and have the option to hook into such offerings.  
Hence it is inevitable for any business enterprise to modernize as much of its IT systems as possible soon, otherwise their competitors will do this and take away market share from them.

## Conclusion

From above discussions, you can see that there is a clear and urgent need for enterprises to embrace modern architectures.  
Hence we are on a cusp of this wave of adoption of distributed architectures (like Microservices) and this trend is about to last for a very long time.

So as the saying goes

> Keep up or be left out....:smile:
