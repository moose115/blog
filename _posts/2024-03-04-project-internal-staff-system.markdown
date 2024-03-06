---
layout: single
title:  "Project: Internal Staff System"
header:
    image: /assets/images/staff-services-chart.png
    teaser: /assets/images/staff-services-chart.png
date:   2024-03-04 08:00:00 -0700
categories: projects
toc: true
---
This post shines some light on one of my projects done during my employment at a health centre. The development process required me to touch concepts of not only software development, but also DevOps and system administration.

## Overview

The purpose of the project is to augment existing tools at the staff's hand and increase convenience of daily tasks. My design includes setting up a self-contained environment for on-premise development and deployment.

Technologies used in the project:
- JavaScript (TypeScript) with Node.
  - Nest.js framework to handle backend in modular microservice-esque fashion.
  - Angular 12 with Ionic and Standalone Components.
- Go.
  - Ory Stack simplified authentication and authorization tremendously.
  - Modification of Ory Kratos which better integrates MS Office account data
- Postgres
- Docker
- Nginx
- Traefik
- Gitlab
- Linux Debian 12
- Windows Server & AD Domain

## Architecture Design

{% include figure image_path="/assets/images/staff-services-chart.png" alt="Chart of the design" caption="Simplified flow of user interactions" %}

### Auth and proxy

The design is rather simple. First, user interactions are routed through a Traefik reverse-proxy. This allows using a single hostname within internal DNS, making managing connectivity much easier. Slap on an API path and strip prefixes.

From there everything simply branches out, each service acting independently. Let's start with authentication and authorization. I opted for Ory stack which made the entire ordeal incredibly easy. I took the opportunity to contribute to Ory Kratos, by pulling more user data from Microsoft OIDC service (see [pull/3609](https://github.com/ory/kratos/pull/3609)). Using Kratos for ID and Keto for permissions meant that I could skip implementing these myself and focus on implementing actual business logic and features for the staff.

### Backend

Opting for Nest, seemingly with batteries included, required me to correctly set up data validation and storage, which was a bit of a mess to deal with (do I use well documented TypeORM or try newish MikroORM that the docs seem to recently recommend?). After that, a few simple auth/role guards and filters and I was done setting up. At this point creating new features is a matter of generating some CRUD operations, services and models. Anything heavy would be handled by a separate service.

### Frontend

Frontend was a wild ride. Coming from React, I chose to use Angular for better maintainability and similarity to Nest, and I figured it is better to keep the stack rather concise. The transition to Angular was not bad at all, especially with Standalone Components, however I decided not to use Signals yet. I may have shot myself in the foot choosing Ionic. I spent more time shuffling docs in order to find why the dev server version shows up just fine, but built is bugged, wonky or otherwise drastically different. PSA: If you generate components with Ionic CLI, even with Standalone settings, generated code imports **IonicModule**. IntelliSense in templates was surely convenient, as every ionic component was a Tab-press away, but build output was simply unusable because of that. So yeah, better import each and every component manually.
In the end, similarly to the backend, make views for each feature and display only services that users are allowed to use (trivial with Ory Keto). Build, serve via Nginx and voila!

Postgres for storage. Works.

That is essentially the core of the project. Each component is dockerized and deployed on a Linux machine within an internal network, with DNS records and SSL certificates available only to Windows machines connected to the domain. If needed, additional services can easily be added and connected with Redis, NATS or other message broker.

## Supporting technologies

To make the environment truly on-premise, I deployed a Gitlab instance and configured a pipeline to streamline development, testing and deployment. With database backups, I find code repositories in a local rack to be quite a bit more secure than big providers that are one account compromise away from a potential outage.

## Takeaways

Thanks to the employers spare resources I had a chance to leave my cloud bubble and tinker with bare metal for the first time. I see the end results as a huge personal success, and certainly as a significant milestone in my career.