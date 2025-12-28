布隆过滤器（Bloom Filter）是一种**概率型数据结构**，非常节省空间，用于快速判断一个元素**可能存在**或**一定不存在**于一个集合中，有“可能存在”（**[假阳性](https://www.google.com/search?q=%E5%81%87%E9%98%B3%E6%80%A7&oq=%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8&gs_lcrp=EgZjaHJvbWUqBggAEEUYOzIGCAAQRRg7Mg4IARAjGBMYJxiABBiKBTIMCAIQIxgnGIAEGIoFMgcIAxAAGIAEMgcIBBAAGIAEMgYIBRBFGD0yBggGEEUYPTIGCAcQRRg90gEIMzY0MmowajeoAgCwAgA&sourceid=chrome&ie=UTF-8&mstk=AUtExfCiJ2ZzrEWz2OHdDpqu8VRYsi36_k6Px340Y1-c75gKF9_pd0_jFT8GSArmrS_4vhhp88gFz4l3N5boBcS1znKQtdiiAmxbs_NpBsVHKm9s6sgm-FfpEfmedz8UHo_kATULx_ct0vxI7PEDZhzMOmwIAHCNiuWHXwrHAvEpVS77Y10&csui=3&ved=2ahUKEwjA4YratNuRAxVyl-4BHdzEGtQQgK4QegQIARAB)**）但永不“一定不存在”（**[假阴性](https://www.google.com/search?q=%E5%81%87%E9%98%B4%E6%80%A7&oq=%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8&gs_lcrp=EgZjaHJvbWUqBggAEEUYOzIGCAAQRRg7Mg4IARAjGBMYJxiABBiKBTIMCAIQIxgnGIAEGIoFMgcIAxAAGIAEMgcIBBAAGIAEMgYIBRBFGD0yBggGEEUYPTIGCAcQRRg90gEIMzY0MmowajeoAgCwAgA&sourceid=chrome&ie=UTF-8&mstk=AUtExfCiJ2ZzrEWz2OHdDpqu8VRYsi36_k6Px340Y1-c75gKF9_pd0_jFT8GSArmrS_4vhhp88gFz4l3N5boBcS1znKQtdiiAmxbs_NpBsVHKm9s6sgm-FfpEfmedz8UHo_kATULx_ct0vxI7PEDZhzMOmwIAHCNiuWHXwrHAvEpVS77Y10&csui=3&ved=2ahUKEwjA4YratNuRAxVyl-4BHdzEGtQQgK4QegQIARAC)**）的特点，适用于缓存穿透、URL去重、垃圾邮件过滤等场景。它由一个**位数组**（bit array）和**多个哈希函数**组成，通过将元素映射到数组的多个位置并置位来记录，查询时检查对应位置是否都为1。

**写入bloom filter的id查询是一定存在的，但是没有写入bloom filter的id查询是不一定不存在（有可能存在（误判）有可能不存在）**

1. 你预热时把 shopId=10 放进布隆。对应的 3 个 bit 都置为 1。
2. 查询 shopId=10：
    - 检查 3 个 bit 都是 1 → 判定“可能存在” → 继续查缓存/DB，确实有店。
3. 查询 shopId=999999（从未写入布隆）：
    - 3 个 bit 中至少有 1 个是 0 → 判定“一定不存在” → 直接返回 nil，不访问 DB。
4. 查询 shopId=12345（布隆未写入，但碰巧 3 个位都被别的 ID 设置过）：
    - 3 个 bit 都是 1 → 判定“可能存在”（其实是误判）→ 继续查 DB，发现没有店，这就是布隆的假阳性。