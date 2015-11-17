During the test and user usage of ngrinder, we found that the test running TPS result is not very stable.

Test environment:

agent: 64bit 12 cores server.

test configuration: process count as 1, thread count as 1. duration is 5 minutes.
 
### Test 1 – with default VM setting
The TPS graph is as below:

TPS:1537.9

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb.png)

We can see that every several seconds, the TPS will drop.

For this scenario, the first thing we though about is GC. So I checked the GC status of the test running process on that agent. And the status is as below:

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_1.png)

Total and used heap size:

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_11.png)

We can see several things:

1. The minor GC runs 25 times, and used 15.724 seconds. And I found that during the GC is working, this process is almost stop working.
2. The heap size used is about 3.5G. The eden area is about 2.6G. For every minor GC, the JVM will collect this 2.6G area. And it takes a long time. (The max heap size is 8G)
3. The full GC runs 2 times, but the old generation area is always increasing.
 
### Test 2 – with concurrent GC policy
Based on the result in above, the first thing I did is to use another kind of GC policy. Then I added “-XX:+UseParNewGC -XX:+UseConcMarkSweepGC” in the jvm args of grinder properties.

TPS: 1493.2

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_3.png)
 
![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_2.png)

Total and used heap size:

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_10.png)

The result:

1. minor GC runs 800 times, used 14.581 seconds.
2. Used heap size is about 130M. (The max heap size is about 130M)
3. Full GC runs 140 times and uses 0.774 second.

We can see that, it only uses 130M memory, and the TPS is almost same as before. But I don’t know why, the max heap size is about 130M.
 
### Test 3 – set heap size and concurrent GC

Then I set the heap size and tested again. The VM argument is: “-Xms256m -Xmx512m -XX:+UseParNewGC -XX:+UseConcMarkSweepGC”

TPS:1495.7

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_4.png)

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_5.png)

Total and used heap size:

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_9.png)

The result is almost same as above, and the full GC time used is much less.

And the used heal size is about 277M, and is not increased to the max heap size(512M) I set. It is just a little more than 256M.
 
### Test 4 – set heap size and default GC
At last, I tested again with default GC policy and set the heap size, ““-Xms256m -Xmx512m”.

TPS:1308.3

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_6.png)

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_7.png)

Total and used heap size:

![](http://www.cubrid.org/files/attach/images/379199/971/489/image_thumb_8.png)

result:

1. minor GC runs 131 times, used 10.086 seconds.
2. no full GC. Old generation is always increasing. Maybe if the test runs a longer time, there will be full GC.
3. TPS is a little less than previous one. And the value is not very stable. The changing range is about: 125 (1475 ~ 1600) .

Conclusion:

1. Test execution will not use too much memory. We can set a max heap size as 512M.
2. In “-server” mode of JVM, the default GC policy is: “-XX:+UseParallelGC  -XX:ParallelGCThreads=10”. If only the heap size is not too big, GC policy doesn’t affect too much. But the default GC policy will also cause some pause time. It will make the TPS value not very stable. So it is suppested to use “-XX:+UseParNewGC -XX:+UseConcMarkSweepGC”.