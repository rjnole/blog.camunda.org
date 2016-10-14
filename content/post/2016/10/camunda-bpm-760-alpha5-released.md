+++
author = "Christopher Zell, Daniel Meyer"
categories = ["Execution"]
date = "2016-10-14T12:00:00+01:00"
tags = ["Release Note"]
title = "Camunda BPM 7.6.0-alpha5 Released"
+++

Camunda 7.6.0-alpha5 is here and it is packed with new features. The highlights are:

* Implementation of the BPMN Conditional Event
* Batch Cancellation of Historic Process Instances
* Huge performance improvements due to caching of Scripting Engines and Compiled Scripts in DMN Engine
* Expressions in Signal and Message Event Names
* Cockpit Usability Improvements
* Pluggable Deployment Cache
* [10 Bug Fixes](https://app.camunda.com/jira/issues/?jql=issuetype%20%3D%20%22Bug%20Report%22%20AND%20fixVersion%20%3D%207.6.0-alpha5)

The [complete release notes](https://app.camunda.com/jira/secure/ReleaseNote.jspa?projectId=10230&version=14692) are available in Jira.

You can [Download Camunda For Free](https://camunda.org/download/)
or [Run it with Docker](https://hub.docker.com/r/camunda/camunda-bpm-platform/).

<!--more-->
# BPMN Conditional Event 

This release introduces support for the BPMN Conditional Event. This is the last BPMN 2.0 even type which was not already supported by the Camunda Engine. The Conditional Event provides usecases such as

* waiting in the process until a certain condition is fulfilled,
* canceling an activity or scope when a condition is fulfilled,
* creating a new token when a condition is fulfilled.

This is a extremely powerful features which allows users to build very dynamic and event driven processes.

TODO: Example

# Bulk Delete of Historic Process Instances

This feature has been requested quite frequently by our users: *cleaning up historic data* after it is not needed any more. This release introduces both an API and a Cockpit Plugin (UI) to batch delete historic Process Instances based on either a list of Ids or a search query:

{{< figure class="main teaser" src="cockpit-batch-delete.png">}}

This feature is part of the bigger "Batch Operations" cluster in the context of which we have already provided the possibility to cancel large numbers of running process instances. Currently on it's way but not yet part of this release is the feature to set job the retires as batch.

# Performance Improvements of DMN Engine

A potential customer evaluated our DMN Engine against another very popular open source rule engine in a project. We were already a lot faster but when they showed us the numbers we thought that this could be better still.
So we introduced caching of Script Engines and Compiled Scripts (Groovy, ...) which made it even faster.

Caching of Compiled scripts is something which we already support for some time in BPMN and now users can enjoy the same benefits and fast performance in DMN.

As a result, we could process the workload (500K evaluations of a decision table) in 12 seconds compared to 76 seconds without the caching. (By comparison, the other open source rule engine took over 2 minutes longer.)

This is the second performance improvement we bring to the DMN engine in the context of the 7.6.0 release cycle.

# Expressions in Signal and Message Event Names

It is now possible to use expression names in signal and message event names. This allows subscribing to messages and signals for which the name is only known at runtime. It enables advanced workflow patterns like synchronizing tokens on different branches or canceling groups of process instances based on a common key.

Consider the following example:

{{< figure class="main teaser no-border" src="signal-events-interrupts-parallel-branch.png">}}

The signal thrown on the upper branch is supposed to cancel the task on the bottom branch. This always worked, but the problem with using signals is that they are broadcast. So while the signal sent from the upper branch would effectively cancel the task on the bottom branch in the current process instance, it would also trigger all catching signal events with the same name in all other process instances.

This release introduces the possibility to use expressions in signal names and by this allows to scope the signal to the current process instance:

```xml
<signal name="cancel-${execution.processInstanceId}" />
```

The documentation has the details for [message events](https://docs.camunda.org/manual/latest/reference/bpmn20/events/message-events/#expressions) and [signal events](https://docs.camunda.org/manual/latest/reference/bpmn20/events/signal-events/#expressions).

# Cockpit Usability Improvements

This release adds a couple of usability improvements to cockpit.

For example, all search and filter bars now use the same search widgets for consistency:

{{< figure class="main teaser no-border" src="cockpit-search-widget.png">}}

# Pluggable Deployment Cache

It is now possible to plugin custom implementations for the caches used for BPMN Process Definitions, CMMN Case Definitions and DMN Decision Definitions. Also, the default cache implementation has now a configurable setting for eviction to prevent out of memory errors in scenarios where users deploy tens of thousands of process definitions.

Read more about it in the [docs](https://docs.camunda.org/manual/latest/user-guide/process-engine/deployment-cache/).

# What's next?

We are getting closer to the 7.6.0 release which is scheduled for November 30, 2016. The last larger open topic is support for DMN DRDs (Decision Requirements Diagrams) in Modeling and Cockpit. Excution Support for DRDs has been added with 7.6.0-alpha2 and we are now working on bringing them into monitoring (Cockpit) and the Modeling tools (bpmn.io / Camunda Modeler).

# Feedback Welcome

Please try out the awesome new features of this release and provide feedback by commenting on this post or reaching out to us in the [forum](https://forum.camunda.org/).
