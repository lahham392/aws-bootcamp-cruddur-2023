# Week 1 — App Containerization
The first question we should ask ourselves, why we need to use the containers if we have already the code and we can run immediately?

Answer is: 
* The size of docker image is much smaller because they just have to implement one layer.
* You can run and start docker containers much faster than the VM.
* You can run a VM image of any OS on any other OS host, you can not do that in docker, because your docker image may not compatible with Kernel in the host “for windows below 10”. You can solve this issue using Docker Toolbox.
