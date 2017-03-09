---
layout: post
title: Serverless and GraphQL at Bustle
date: 2017-03-08
author: a.johnston
categories: update cardano qcon serverless
description: Notes from presentation by Steve Faulkner from Bustle at QCon London 2017.  He talks about replacing all of their services with a serverless architecture.
---

# What is Serverless
Another one of the buzzwords at this years [QCon london](https://qconlondon.com).  The name [Serverless](https://en.wikipedia.org/wiki/Serverless_Framework) is a little misleading, think of it more as Functions-aaS, Functions as a Service.  The term Serverless refers to the fact that you don't deploy or manage a server or service, just the code that you want to run and the platform takes care of running it for you based on certain external events.  The cost savings can be very significant as you only pay for the actual CPU time your function is running, not for a service to be sitting their idle.  It can also scale "magically" to enormous scales and on demand.

Another key point for me was their use of GraphQL, which we too are starting to use and realising the power of it.  They like the GraphiQL interface which provides a common way to document and interact with an API, plus type safety, validations, easy querying on a field by field basis.  Think of it like SQL for AP's.  Before SQL each vendor had their own language, the same is true now for API's but GraphQL changes that.

The full presentation from Steve is available on the [QCon Site](https://qconlondon.com/london-2017/presentation/hitchhikers-guide-serverless-javascript).

# Key Takeaways
These are my bullet points from Steve's presentation.
* Bustle - 50 million users per month.
* Not "Serverless", there are servers but not platforms.
* We have long had infrastructure platforms but not business logic platforms.
* Functions-aaS.
* Deploy functions to the cloud.
* Talking about AWS.
    * Lambda (Functions-aaS) and API Gateway (Routing-aaS).
* Only pay per request.
* Scales "Magically".
* Lambda
    * Write code.
    * Upload
    * Run.
    * Node, python, java, c#, go.
    * Versioning.
    * Aliases.
    * RAM + CPU.
    * Logging.
    * Non-HTTP events.
* API Gateway
    * Uses Swagger.
    * Cache/throttle.
    * Authorisation.
    * Api keys.
    * Logging/metrics.
    * SSL.
* How used in Bustle.
    * 10-20 million lambda calls per day.
    * Costs less then old EC2 setup.
    * Performance is good but not quite as good as EC2 but good enough for business.
    * Frontend
        * HTML + JS.
        * API gateway.
        * S3.
        * Started with Ember or fast boot.
        * Now using Preact.  Most of what react does but lighter.  Content site so good enough for them.  They are mostly static content with little interaction.
        * Why Preact
            * Components - Much nicer way, share code, etc.
            * simple
            * small
    * Backend
        * GraphiQL.bustle.com
        * JSON
        * API Gateway
        * Lambda
        * Redis
        * Started with REST/Javascript
        * Then moved to purescript.  Not using purescript anymore. Was hard for their team to be productive in purescript/hascal.  Hard for whole team.
        * Now using GraphQL
        * Why GraphQL
            * Originally there was resistance from team.
            * Now they think it is the most amazing thing ever and everyone should use it.
            * GraphiQL.
                * API Explorer.
                * Number 1 reason to write graphical.
                * Gives more strictness in documentation.
                * One true way to write api Explorer.
            * Types
                * Gives strong types.
                * Checks queries.
            * Field Level resolution
                * Makes you think about your data in a more field specific way rather than just send whole database record.
            * [Dataloader](https://github.com/facebook/dataloader)
                * Separate project.
                * Batches up promises. Schedules them.
                * Batches requests for field level resolution.
                * Then batches up request to the database in one request.
    * Serverless
        * Ops
            * Less time worrying about DevOps.
            * Benchmarks.
            * Fallbacks.
            * Load testing.
            * Monitoring.
        * Scale.
        * Interaction.
            * Single function deployment.
        * Cost.
            * Costs less
            * First thing they rewrote was node.js to lambda.
            * Copied and pasted from EC2 to lambda
            * From $2,500 per month to $400 per month.
            * There traffic is spike so works out cheaper.
    * Why not.
        * Lock in.
            * Not seen this as an issue.
            * Can server with express if you want to.  Easy small script.
        * Cold function.
            * Falls out of cache if not used a lot.
            * Node and python are ok as not much startup.  JVM is more of an issue.
        * Be careful of calling function outside of main handler.
        * Long running tasks.
            * 5 minute max on any tasks.
        * Testing.
            * End to end testing is difficult.
            * Test in the cloud.  Testing against live stuff.
        * Tools.
            * Lot of monitoring and tools don’t work. Such as shell commands.
            * Some new tools becoming available though.
            * Still new industry.
        * Frameworks.
            * Serverless framework.
            * [Node lambda](https://www.npmjs.com/package/node-lambda) command line tool to locally run and deploy your node.js application to Amazon Lambda.
            * [Apex](http://apex.run) (If starting today would use this).
            * [Claudia.js](https://github.com/claudiajs/claudia) tp deploy projects.
        * [Shep](https://github.com/bustlelabs/shep).
            * Their npm module.
            * Javascript only.
            * Swagger.
            * Webpack.
            * Environments.
* Azure
    * Have to select maximum scale.
    * Routing is slightly better

For full details see [Steve Faulkner's excellent presentation](https://qconlondon.com/london-2017/presentation/hitchhikers-guide-serverless-javascript).
