框架/工具/产品在实现的时候，都考虑到了容灾，扩展，负载均衡，于是出现一个配置中心(ConfigServer)的东西来解决这些问题。

![avatar](./resource/%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83.gif)

# ConfigServer
和每个Server/Client之间会作一个实时的心跳检测（因为它们都是建立的Socket长连接），比如几秒钟检测一次。收集每个Server提供的服务的信息，每个Client的信息，整理出一个服务列表.

当某个Server不可用，那么就更新受影响的服务对应的serverAddressList，即把这个Server从serverAddressList中踢出去（从地址列表中删除），同时将推送serverAddressList给这些受影响的服务的clientAddressList里面的所有Client。

当某个Client挂了，那么更新受影响的服务对应的clientAddressList.

新加一个Server时，由于它会主动与ConfigServer取得联系，而ConfigServer又会将这个信息主动发送给Client，所以新加一个Server时，只需要启动Server，然后几秒钟内，Client就会使用上它提供的服务.

# Server
真正提供服务的机器，每个Server启动时，主动与ConfigServer建立Scoket长连接，并将自己的IP，提供的服务名称，端口等信息直接发送给ConfigServer，ConfigServer就会收集到每个Server提供的服务的信息。

# Client
调用服务的机器，每个Client启动时，主动与ConfigServer建立Socket长连接，并将自己的IP等相应信息发送给ConfigServer。

Client在使用服务的时候根据服务名称去ConfigServer中获取服务提供者信息（这样ConfigServer就知道某个服务是当前哪几个Client在使用），Client拿到这些服务提供者信息后，与它们都建立连接，后面就可以直接调用服务了，当有多个服务提供者的时候，Client根据一定的规则来进行负载均衡，如轮询，随机，按权重等。

一旦Client使用的服务它对应的服务提供者有变化（服务提供者有新增，删除的情况），ConfigServer就会把最新的服务提供者列表推送给Client，Client就会依据最新的服务提供者列表重新建立连接，新增的提供者建立连接，删除的提供者丢弃连接.

优点：
* 只要在Client和Server启动的时候，ConfigServer是好的，服务就可调用了，如果后面ConfigServer挂了，那只影响ConfigServer挂了以后服务提供者有变化，而Client还无法感知这一变化。
* Client每次调用服务是不经过ConfigServer的，Client只是与它建立联系，从它那里获取提供服务者列表而已
* 调用服务-负载均衡：Client调用服务时，可以根据规则在多个服务提供者之间轮流调用服务。
* 服务提供者-容灾：某一个Server挂了，Client依然是可以正确的调用服务的，当前提是这个服务有至少2个服务提供者，Client能很快的感知到服务提供者的变化，并作出相应反应。
* 服务提供者-扩展：添加一个服务提供者很容易，而且Client会很快的感知到它的存在并使用它。
