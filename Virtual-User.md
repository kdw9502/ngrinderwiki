Virtual Users in nGrinder is mapped into threads. If there are 1000 threads which runs a script, which means there are 1000 virtual users. nGrinder has a little different approach from other performance test tools. Instead of mimicking the user real behavior, we focus on how to make the server reach its limit. So we don't recommend to use think time concept in the scripts.

However if you want to simulate real user behaviors and really want to run the script with the huge count of virtual users, follow the below instructions. For example... if you want to have 10000 vusers,
>
1. Prepare the 2 good agent machines. You may need machines with 2 cores and 6G memory.
2. Write the script with Groovy (Groovy script is more efficient than Jython)
3. Increase the vuser limit by setting agent.max.vuser to 5000 in system.conf file.
4. Simulate thinking time with grinder.sleep(how_much_sleep_in_milliseconds) in the script
5. When you configure the vuser count, please set the process around 10 and thread around  500.

It is always possible for a test to cause the memory related problem. So you need to set process and thread count very carefully.

Please refer [Process And Thread](process-and-thread) for detail.  If you need more vusers, please consider add more agents.

Actually in our company NHN, we set the vuser limit as 2000 with 4GB ram and 2 core CPU agent machines. nGrinder agents are very stable with that number. We expect that 8000 vusers can be run if you have 8GB and 4 core CPU machines. 

If you don't know how much vusers you should use, there is a good vuser calculator in [our competitor's site](http://www.webperformance.com/library/tutorials/CalculateNumberOfLoadtestUsers/).    It's really good products. :-) 