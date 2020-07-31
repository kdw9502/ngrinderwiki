This chapter describes about the nGrinder server mode agent.

Legacy nGrinder agents must be accessible to nGrinder controller via TCP. Therefore, it was difficult to set up a private agents to connect to the controller behind a firewall.

Now, nGrinder provides the server mode agent. You can easily connect the private agent to the nGrinder controller behind a firewall with the server mode agent.
This can be helpful when the agent is running on a cloud computing platform like AWS and the controller is running on a private network.

The server mode agent needs the public IP and an accessible TCP port, because the server mode agent make connection from controller to agent, as opposite as normal mode agent.

If your server mode agent has public IP differ to private IP(like in case running on AWS and access to agent via public IP), you need to set up broadcast IP to let the controller knows which IP address it needs to access. You can set the broadcast IP address in agent.conf as below.

```
agent.broadcast_ip=${your_public_ip}
```

## Start Server Mode Agent

1. Edit `${NGRINDER_AGENT_HOME}/agent.conf`
    ```
    agent.connection_mode=controller_to_agent
    #agent.connection_port=14000
    #agent.broadcast_ip=
    ```
    Set `agent.connection_mode` to `controller_to_agent` to run agent as server mode. By default, agent tries to connect to controller.
    Set `agent.connection_port` to available port, if you want to specify agent port to be opened. By default, `14000` is opened to listen controller connection.
2. Run the agent
    ```
    ./run_agent.sh
    ```
    if you see the agent log `waiting for connection on ${IP_ADDRESS}:${PORT}`, it has run successfully.
3. Add server mode agent on agent management page
![Screen Shot 2020-07-13 at 11 32 37](https://user-images.githubusercontent.com/10591327/87514351-114f2080-c6b5-11ea-9b95-4995c936d899.png)
![Screen Shot 2020-07-13 at 11 32 21](https://user-images.githubusercontent.com/10591327/87514345-101df380-c6b5-11ea-8d99-03ae9179c006.png)
