# fancyss HND V8 3.5.30.modify:wyhydy.0.3 升级说明

## 发布信息

- 插件显示版本：`3.5.30.modify:wyhydy.0.3`
- 安装包：`fancyss_hnd_v8_wyhydy_0.3.tar.gz`
- 适用平台：HND V8 / ARMv8
- SHA-256：`3077eef263fd5e24c801875ee649b229ac975138454a16d7325e158a6d660761`
- 安装包大小：`22,202,401` 字节

本版本为基于 fancyss 3.5.30 的累计修改版，包含以下改进。

## 1. AnyTLS 后端与启动稳定性

- AnyTLS 后端使用 `anytls-go anytls/0.0.13 fancyss-compat`。
- 为兼容现有 HND V8 安装结构，部署包及路由器中的二进制文件名仍为 `/koolshare/bin/anytls-zig`；源码项目保持 `anytls-go` 命名。
- 新增专用的 `run_bg_detached()` 启动函数，仅用于 AnyTLS 模式下的 `anytls-zig` 和 `ipt2socks` 两个长期进程。
- 使用 `nohup` 并将标准输入连接到 `/dev/null`，防止通过 SSH 执行插件启动或重启后，关闭 SSH 导致两个进程收到终端挂断信号并退出。
- 通用 `run_bg()` 保持原逻辑，不改变其他代理核心和短任务的进程生命周期。
- 本修改不会增加 worker 数量，也不会创建守护循环；只改变两个既有进程对终端挂断信号的处理，因此不会因 `nohup` 本身增加持续内存占用或引入新的 OOM 机制。

## 2. SmartDNS 启动顺序修复

- 修复机场专属 DNS 运行文件在域名刷新清理后没有及时重建的问题。
- 在生成 SmartDNS 运行配置前，重新生成机场 DNS 上游主机的直连 bootstrap 文件。
- 避免 DoH/上游 DNS 域名错误进入代理默认组，形成“DNS 解析依赖 AnyTLS、AnyTLS 启动又依赖 DNS”的循环。
- 解决订阅成功但节点域名无法解析、所有节点表现为不可用的问题。

## 3. 订阅配置状态修复

- 当订阅配置尚无 DBus 状态记录时，以空 JSON 对象 `{}` 初始化状态。
- 修复计划任务实际执行后，页面中的“最近结果”和“上次成功”仍显示“尚未同步”的问题。
- 保留失败任务的现有节点，不因单次订阅下载失败清理本地订阅来源。

## 4. 规则源和规则更新流程

- 传统规则源改为 qxzg/Actions：
  `https://raw.githubusercontent.com/qxzg/Actions/3.0/fancyss_rules_ng`
- 只修改 `ss_rule_update.sh` 的 `URL_MAIN`；插件自身升级地址保持为：
  `https://raw.githubusercontent.com/hq450/fancyss/3.0/packages`
- 规则下载在本地 SOCKS5 出口健康时优先使用代理访问 GitHub Raw，并保留直连 `curl` 和 `wget` 兜底。
- 每个规则文件下载后进行 MD5 校验；任意更新项失败时恢复整批旧规则，防止应用不完整规则集。
- 更新前创建临时备份，更新后执行本地状态和代理出口健康检查；异常时自动回滚。
- 使用规则热加载，保持代理核心 PID 和已有连接不变。
- 热加载不再调用 `restart_dnsmasq`，避免部分梅林固件触发 `nat-start`、短暂删除 `SHADOWSOCKS` 链后造成持续断流。

## 5. 页面样式

- ROG 皮肤下 `.ss_btn` 宽度调整为 `18%`，缩小“科学上网开关”和“插件运行状态”左侧列宽。
- 调整“更新日志、插件帮助、分流检测、详细状态”四个按钮的布局，使其保持正常尺寸并右对齐，不遮挡版本信息。
- 插件页面显示完整版本：`3.5.30.modify:wyhydy.0.3`。


## 升级建议

1. 在路由器页面中备份现有节点和插件配置。
2. 上传并覆盖安装 `fancyss_hnd_v8_wyhydy.tar.gz`。
3. 安装完成后启动插件，确认页面显示版本 `3.5.30.modify:wyhydy.0.3`。
4. 检查 AnyTLS 节点可用、国外连接正常，并执行一次规则更新验证 qxzg 规则源。
