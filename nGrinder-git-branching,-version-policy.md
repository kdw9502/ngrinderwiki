### Branching policy:
Now we are developing version 3.1, while maintaining version 3.0 at the same time. For this  we need branching. The branching policy will be as  follows:

We will work on a master for version 3.1 version, and create a branch for version 3.0.

We`ll fix some bug and release, we will merge this into master.


For 3.0 maintaining, we will release a patch version in 1~4 days depending on the importance and difficulty of feature.

![](http://www.cubrid.org/files/attach/images/379199/156/480/branch.gif)

In the future, we should also follow this policy for branching.


### Version policy:
In ngrinder, there will be several kinds of  version used:  
X.0:  
It should be significant upgrade and must follow the full developing process, with phase of alpha, beta. Like 3.0 version.  
X.x:  
It should be the version of some bug fix and function improvement. To release this version, we should follow the QA test and get QA's verification to release.  
X.x.x  
It will be some little bug fix, and will be release as soon as possible if only user posted some bugs. For this version, we will release it without QA's verification.