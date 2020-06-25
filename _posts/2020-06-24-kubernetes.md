---
layout: page
title: Kubernetes入门实践
---

## 什么是kubernetes？
> Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.

## pros
* Service discovery and load balancing
* Storage orchestration
* Automated rollouts and rollbacks
* Automatic bin packing
* Self-healing
* Secret and configuration management

## discriminations
* Does not limit the types of applications supported. 
* Does not deploy source code and does not build your application. 
* Does not provide application-level services, such as middleware (for example, message buses), data-processing frameworks (for example, Spark), databases (for example, MySQL), caches, nor cluster storage systems (for example, Ceph) as built-in services. 
* Does not dictate logging, monitoring, or alerting solutions.
* Does not provide nor mandate a configuration language/system (for example, Jsonnet).
* Does not provide nor adopt any comprehensive machine configuration, maintenance, management, or self-healing systems.
