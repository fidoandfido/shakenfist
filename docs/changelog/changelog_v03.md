The big ideas of v0.3
=====================

v0.3 is a re-write of portions of v0.2 that we felt were holding Shaken Fist back. The two most obvious examples are that slow operations would often timeout because of our HTTP worker model, and locking simply didn't work at all. Some more detail on those:

* All operations in v0.2 are handled by the REST API HTTP workers (gunicorn). So, if you ask us to launch an instance, we use the HTTP worker to do that thing. That works great, unless the operation is going to take more time than the HTTP worker is allowed to run for. This is pretty easy to achieve if the image you're fetching is large. Instead, we now move to a model where the HTTP worker creates a queued job, and then polls its state for a small period of time before returning. That means if things are quick you get the same API behaviour as before, but for slow operations you'll be told that the job is incomplete but still executing.

* We also realised somewhere late in v0.2's life that the etcd library we were using was pretty buggy. Locking simply didn't work some of the time. Additionally, our locking code was pretty ad hoc and sometimes we would get the names of the locks wrong because they were just strings. This has now been completely re-written, but that has shaken out a number of bugs that are surfaced by locking actually working now. We have worked through those bugs and sought to resolve them.

Changes between v0.3.0 and v0.3.1
=================================

v0.3.1 was released on 21 October 2020.

* Reject power on while powering off. shakenfist/394
* Always use locally administered MAC addresses. shakenfist/409
* Fix unintentionally ignored exceptions. shakenfist/410
* Image downloads should verify checksums where possible. shakenfist/412
* Improved CI reliability. shakenfist/416, shakenfist/418, shakenfist/419, shakenfist/420, shakenfist/426, shakenfist/428
