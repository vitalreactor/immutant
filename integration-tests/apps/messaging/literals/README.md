# Immutant Test App

This app was created to reproduce a potential bug with Immutant. Thanks to the Immutant people in irc! Ya'll rock for helping me with this!

## Brief Summary

I'm trying to edn encode a joda time (clj-time) to put on a queue.  I've defined a custom writer and reader, and I've added the approriate configuration in /src/data_readers.clj.

As you can see from the stacktrace below (generated by src/immutant/init.clj), the clojure read-string can read my custom edn-encoded jodatime. I print it encoded and then again round-tripped.

Then I try to publish the same jodatime, and immutant.messaging/receive errors saying "Invalid edn-encoded data".  It appears immutant.messaging/publish does encode the jodatime correctly (also shows in stacktrace).

## To reproduce

Clone this repo, download deps, deploy, and `lein immutant run`

## Stacktrace

```
13:51:21,412 INFO  [org.jboss.web] (ServerService Thread Pool -- 49) JBAS018210: Register web context: /reader-msg-issue
13:51:23,288 INFO  [immutant.init] (pool-5-thread-1) The data-readers are:  {acme/datetime #'reader-msg-issue.core/from-edn-datetime}
13:51:23,292 INFO  [immutant.init] (pool-5-thread-1) Edn encoded:  #acme/datetime "Thu, 29 Aug 2013 17:51:23 +0000"
13:51:23,296 INFO  [immutant.init] (pool-5-thread-1) Roundtripping:  #acme/datetime "Thu, 29 Aug 2013 17:51:23 +0000"
13:51:23,297 INFO  [immutant.init] (pool-5-thread-1) And the class is:  org.joda.time.DateTime
13:51:23,299 INFO  [immutant.init] (pool-5-thread-1) Start the queue
13:51:23,306 INFO  [org.hornetq.core.server] (pool-6-thread-1) HQ221005: trying to deploy queue jms.queue.queue.jons
13:51:23,311 INFO  [org.jboss.as.messaging] (pool-6-thread-1) JBAS011601: Bound messaging object to jndi name java:/queue/jons
13:51:23,315 INFO  [immutant.init] (pool-5-thread-1) Publish a joda time
13:51:23,382 INFO  [immutant.init] (pool-5-thread-1) Attempt to receive it...
13:51:23,407 ERROR [immutant.runtime] (pool-5-thread-1) Unexpected error occurred loading immutant.init #<RuntimeException java.lang.RuntimeException: Invalid clojure-encoded data (type=class java.lang.String): #acme/datetime "Thu, 29 Aug 2013 17:32:22 +0000">
13:51:23,408 ERROR [org.jboss.msc.service.fail] (pool-5-thread-1) MSC00001: Failed to start service jboss.deployment.unit."reader-msg-issue.clj".immutant.core.application-initializer: org.jboss.msc.service.StartException in service jboss.deployment.unit."reader-msg-issue.clj".immutant.core.application-initializer: java.lang.RuntimeException: Invalid clojure-encoded data (type=class java.lang.String): #acme/datetime "Thu, 29 Aug 2013 17:32:22 +0000"
	at org.projectodd.polyglot.core.AsyncService$1.run(AsyncService.java:52) [polyglot-core.jar:1.15.0]
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:439) [classes.jar:1.6.0_51]
	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:303) [classes.jar:1.6.0_51]
	at java.util.concurrent.FutureTask.run(FutureTask.java:138) [classes.jar:1.6.0_51]
	at java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:895) [classes.jar:1.6.0_51]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:918) [classes.jar:1.6.0_51]
	at java.lang.Thread.run(Thread.java:680) [classes.jar:1.6.0_51]
Caused by: java.lang.RuntimeException: Invalid clojure-encoded data (type=class java.lang.String): #acme/datetime "Thu, 29 Aug 2013 17:32:22 +0000"
	at immutant.codecs$eval4288$fn__4289.invoke(codecs.clj:63)
	at clojure.lang.MultiFn.invoke(MultiFn.java:231) [clojure.jar:]
	at immutant.messaging.codecs$eval4389$fn__4390.invoke(codecs.clj:84)
	at clojure.lang.MultiFn.invoke(MultiFn.java:227) [clojure.jar:]
	at immutant.messaging.codecs$decode_with_metadata.invoke(codecs.clj:106)
	at immutant.messaging.codecs$decode_if.invoke(codecs.clj:115)
	at immutant.messaging$receive$fn__4426.invoke(messaging.clj:133)
	at immutant.messaging.core$with_connection_STAR_.invoke(core.clj:269)
	at immutant.messaging$receive.doInvoke(messaging.clj:125)
	at clojure.lang.RestFn.invoke(RestFn.java:410) [clojure.jar:]
	at immutant.init$eval4665.invoke(init.clj:20)
	at clojure.lang.Compiler.eval(Compiler.java:6619) [clojure.jar:]
	at clojure.lang.Compiler.load(Compiler.java:7064) [clojure.jar:]
	at clojure.lang.RT.loadResourceScript(RT.java:370) [clojure.jar:]
	at clojure.lang.RT.loadResourceScript(RT.java:361) [clojure.jar:]
	at clojure.lang.RT.load(RT.java:440) [clojure.jar:]
	at clojure.lang.RT.load(RT.java:411) [clojure.jar:]
	at clojure.core$load$fn__1451.invoke(core.clj:5530)
	at clojure.core$load.doInvoke(core.clj:5529) [clojure.jar:]
	at clojure.lang.RestFn.invoke(RestFn.java:408) [clojure.jar:]
	at clojure.core$load_one.invoke(core.clj:5336) [clojure.jar:]
	at clojure.core$load_lib$fn__1400.invoke(core.clj:5375)
	at clojure.core$load_lib.doInvoke(core.clj:5374) [clojure.jar:]
	at clojure.lang.RestFn.applyTo(RestFn.java:142) [clojure.jar:]
	at clojure.core$apply.invoke(core.clj:619) [clojure.jar:]
	at clojure.core$load_libs.doInvoke(core.clj:5413) [clojure.jar:]
	at clojure.lang.RestFn.applyTo(RestFn.java:137) [clojure.jar:]
	at clojure.core$apply.invoke(core.clj:619) [clojure.jar:]
	at clojure.core$require.doInvoke(core.clj:5496) [clojure.jar:]
	at clojure.lang.RestFn.invoke(RestFn.java:408) [clojure.jar:]
	at immutant.runtime$init_by_ns.invoke(runtime.clj:58)
	at immutant.runtime$initialize.invoke(runtime.clj:91)
	at clojure.lang.Var.invoke(Var.java:419) [clojure.jar:]
	at org.projectodd.shimdandy.impl.ClojureRuntimeShimImpl.invoke(ClojureRuntimeShimImpl.java:106)
	at org.projectodd.shimdandy.impl.ClojureRuntimeShimImpl.invoke(ClojureRuntimeShimImpl.java:99)
	at org.immutant.core.ApplicationInitializer.startAsync(ApplicationInitializer.java:48) [immutant-core-module.jar:1.0.0]
	at org.projectodd.polyglot.core.AsyncService$1.run(AsyncService.java:49) [polyglot-core.jar:1.15.0]
	... 6 more
Caused by: clojure.lang.ExceptionInfo: No reader function for tag datetime {:type :reader-exception}
	at clojure.core$ex_info.invoke(core.clj:4327) [clojure.jar:]
	at clojure.tools.reader.reader_types$reader_error.doInvoke(reader_types.clj:216)
	at clojure.lang.RestFn.invoke(RestFn.java:439) [clojure.jar:]
	at clojure.tools.reader$read_tagged.invoke(reader.clj:625)
	at clojure.tools.reader$read_dispatch.invoke(reader.clj:50)
	at clojure.tools.reader$read.invoke(reader.clj:697)
	at clojure.tools.reader$read_string.invoke(reader.clj:726)
	at immutant.codecs$eval4288$fn__4289.invoke(codecs.clj:60)
	... 42 more

13:51:23,432 INFO  [org.jboss.as.server] (ServerService Thread Pool -- 26) JBAS018559: Deployed "reader-msg-issue.clj" (runtime-name : "reader-msg-issue.clj")
13:51:23,433 INFO  [org.jboss.as.controller] (Controller Boot Thread) JBAS014774: Service status report
JBAS014777:   Services which failed to start:      service jboss.deployment.unit."reader-msg-issue.clj".immutant.core.application-initializer: org.jboss.msc.service.StartException in service jboss.deployment.unit."reader-msg-issue.clj".immutant.core.application-initializer: java.lang.RuntimeException: Invalid clojure-encoded data (type=class java.lang.String): #acme/datetime "Thu, 29 Aug 2013 17:32:22 +0000"

13:51:23,437 INFO  [org.jboss.as] (Controller Boot Thread) JBAS015961: Http management interface listening on http://127.0.0.1:9990/management
13:51:23,437 INFO  [org.jboss.as] (Controller Boot Thread) JBAS015951: Admin console listening on http://127.0.0.1:9990
13:51:23,437 ERROR [org.jboss.as] (Controller Boot Thread) JBAS015875: JBoss AS 7.2.x.slim.incremental.6 "Janus" started (with errors) in 7687ms - Started 133 of 186 services (1 services failed or missing dependencies, 47 services are passive or on-demand)
```
