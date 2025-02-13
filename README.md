smp-runtime
===========

This repository builds a base image for the app and worker containers, which includes the OS and all related
dependencies. When the main SMP build pipeline runs, all it has to do is pull the latest runtime and copy in the source
code.

The packages do not contain any sensitive or proprietary code (it's all public open source).