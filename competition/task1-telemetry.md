# 赛题一：部署并启动 seekdb 实例

## 部署 UUID（36 位，含连字符）

```
ceae84d7-e8f8-8e93-a5cb-518052ed3647
```

来源：实例运行目录 `run/telemetry.json` 的 `content.id`（实例首次 bootstrap 时由 seekdb 自身生成并上报）。

## 部署信息

| 项目 | 内容 |
| --- | --- |
| 部署时间 | 2026-10-31 21:41（Asia/Shanghai） |
| 部署平台 | Ubuntu 26.04.1 LTS x86_64（VMware 虚拟机，2 vCPU / 10 GB 内存 / 100 GB 磁盘） |
| seekdb 版本 | 1.4.0.0（Ubuntu 24.04 amd64 deb 包 seekdb_v140.deb） |
| 安装包来源 | `http://mirrors.oceanbase.com/oceanbase/community/stable/ubuntu/noble/amd64/` |
| 运行目录 | `/var/lib/seekdb`（data-dir `/var/lib/seekdb/store`，redo-dir `/var/lib/seekdb/store/redo`） |
| 配置文件 | `/etc/seekdb/seekdb.cnf` |
| 启动方式 | `systemctl start seekdb`（`seekdb.service`，Type=notify，已 `systemctl enable`） |
| 监听端口 | 2881（MySQL 协议） |
| 服务状态 | `systemctl is-active seekdb` → `active` |

## 启动参数（`/etc/seekdb/seekdb.cnf`）

```
base-dir=/var/lib/seekdb
data-dir=/var/lib/seekdb/store
redo-dir=/var/lib/seekdb/store/redo
port=2881
cpu_count=4
log_disk_size=4G
datafile_size=4G
memory_limit=4G
```

## 实例可用性验证

通过 MySQL 协议连接（`mysql -h127.0.0.1 -P2881 -uroot`）执行 SQL，结果如下：

- `select version()` 返回 `5.7.25-OceanBase seekdb-v1.4.0.0`
- `select @@server_uuid` 返回 `c48ed3c1-d530-11f1-b4d3-000c290a632e`
- 建库 `demo`、建表 `hello(id int primary key, msg varchar(50))`、写入并查询成功，返回 `(1, 'hello seekdb')`
- `show databases` 返回 `demo, information_schema, mysql, oceanbase, test`

## run/telemetry.json 原文

```json
{"content": {"id": "ceae84d7-e8f8-8e93-a5cb-518052ed3647", "host": {"os": "bunt", "cpu": "Intel(R) Core(TM) i7-14650HX", "cpuCount": 2, "osVersion": "26.04", "memorySize": "8.95170593G"}, "event": "bootstraped", "version": "1.4.0.0", "instance": {"port": 2881, "hostHash": "12CA17B49AF2289436F303E0166030A21E525D266E209267433801A8FD4071A0", "createTimestamp": 1793454086578688}, "reporter": "systemd", "resource": {"cpuCount": 4, "logDiskSize": "4G", "memoryLimit": "7.16136475G", "dataFileSize": "4G"}, "telemetryVersion": 5}, "component": "seekdb"}
```

## 部署过程要点

1. 从官方镜像下载 seekdb 1.4.0 Ubuntu amd64 deb 包，`dpkg -i` 安装。
2. 为避免与已有数据/磁盘容量限制冲突，为虚拟机扩容磁盘并把根分区扩展到 100 GB（`/` 可用空间约 39 GB）。
3. 清理上次失败遗留在 `/var/lib/seekdb/etc` 的 `seekdb.data_version.bin`（残留会导致 `check_server_empty` 判定“实例非空”，bootstrap 直接被拒，报 `OB_ERR_SYS`），同时清空 `store/`、`log/`、`run/`。
4. 调大初始化参数（`datafile_size=4G`、`log_disk_size=4G`），`systemctl start seekdb` 完成 bootstrap，服务进入 `active`，`run/telemetry.json` 生成。
