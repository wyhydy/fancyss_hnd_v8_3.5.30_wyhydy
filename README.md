
# 解决了 ，fancyss 在 Anytls 协议下，发生节点断流，或者 无法解析 节点的问题。 
# 基于  fancyss_hnd_v8_full_3.5.30.tar.gz 修改
- 1、替换了 anytls-zig 为 anytls-go
- 2、 anytls-go 启用了 -dr，关闭 Session 复用。每个代理连接结束后，对应 Session 会立即关闭，并从内部 sessions Map 删除；Stream Map 也会清理。不会无限期堆积正在拨号的连接。 大幅度缓解OOM/内存泄漏。
- 3、修复了，订阅后的所有节点因为 SmartDNS 配置生成顺序错误，无法解析节点域名。
- 4、调整了页面布局，增大了版本号的显示区域。

理论上支持所有 armv8 架构的路由器。我的路由器是 gt-ac5300 ，在这个路由器下做的测试和联调，OK。
版本信息为：3.5.30.modify:wyhydy

使用方法： 
- 1、暂停原始的fancyss 运行。
- 2、卸载原始的fancyss控件。
- 3、离线安装本控件，方式方法和 安装 fancyss 一致。
