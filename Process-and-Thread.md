nGrinder uses processes and threads to simulate multiple users. For example, if you set up a test like following. Only one agent will be activated and 1 process will be invoked then this process will include 2 running threads. Each thread acts like a user. So 2 virtual users are running. If you increase agent count to 2, you will have 4 vusers in total.

![](assets/Process-and-Thread-113f5.png)

If you put the virtual user count in Vuser per agent field, nGrinder calculates the appropriate processes and threads. For example if you put 100 in the field, it will turn to 99. It’s because 3 processes and 33 threads will be used. You can configure process-thread count calculation logic by configuring process_and_thread_policy.js.

Processes more than 10 easily cause thrashing by memory swapping in the agent if your agent has less than 4GB memory. If there are many number of processes, they would exceed the agent machine’s physical memory size. Please set it less than 10. If you increase the thread number more than 200, it might cause problems either. The threads belong to one process share the same heap. It’s obvious to see the OOM error if a lot of threads occupies the process memory.

Therefore, you have to set process and thread count with care. If you have no idea on this, Just let the nGrinder calculate automatically.

NEWS!! we have some experiment how many vusers a agent can handle. 4000 vusers can be supported by a single 4GB memory agent if each test doesn't use takes much memories. If you like to increase the max vuser limitation, please configure agent.max.vuser in system.conf.
