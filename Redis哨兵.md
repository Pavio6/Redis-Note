**Redis 哨兵（Sentinel）** 是 Redis 提供的一种 **高可用性（HA）** 解决方案，它通过监控 Redis 实例的运行状态，帮助自动化处理故障转移、故障检测和通知等任务，从而确保 Redis 集群的高可用性和数据可靠性。

**作用**：
- **监控**：Sentinel会不断检查master和slave是否按预期工作
- **自动故障恢复**：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主
- **通知**：Sentinel充当Redis客户端的服务发现来源，当集群发送故障转移时，会将最新信息推送给redis客户端

Redis 哨兵由多个 **哨兵实例（Sentinel）** 组成，它们协同工作，形成一个分布式系统。每个哨兵实例都负责 **监控** 和 **管理** 集群中的 Redis 主从节点。