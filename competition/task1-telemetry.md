# 赛题一：部署并启动 seekdb 实例

## 部署 UUID（36 位，含连字符）

```
1358a638-1ead-11b2-b492-760894dafa77
```

来源：实例系统变量 `server_uuid`（`select @@server_uuid` 返回，与运行目录 `store/` 数据文件内记录的部署标识一致）。

## 遥测 ID（提交到提测平台的值）

```
D45A775DEC11CC267964E0953561BE4F7415EA81CC771EF0F0B7B16D8B22800A
```

## 部署信息

| 项目 | 内容 |
| --- | --- |
| 部署时间 | 2026-10-31 16:28（Asia/Shanghai） |
| 部署平台 | Windows 11（x86-64，Intel i7-14650HX，24 核 / 15.7 GB 内存） |
| seekdb 版本 | 1.3.0.0（Windows 包 seekdb-1.3.0.0-100000092026051510-win64.msi） |
| 运行目录 | `F:\dl\temp_20261031_01\seekdb\instance` |
| 启动参数 | `seekdb.exe --base-dir=<运行目录> --port=2881 --parameter memory_limit=2G --nodaemon` |
| 监听端口 | 2881（127.0.0.1） |

## 实例可用性验证

通过 MySQL 协议连接并执行 SQL，结果如下：

- `select version()` 返回 `5.7.25-OceanBase seekdb-v1.3.0.0`
- 建表 `demo_hello(id int primary key, msg varchar(50))`、插入并查询成功，返回 `(1, 'hello seekdb')`
- `show databases` 返回 `information_schema, mysql, oceanbase, sys_external_tbs, test`

## run/telemetry.json 原文

```json
{
  "content": {
    "id": "D45A775DEC11CC267964E0953561BE4F7415EA81CC771EF0F0B7B16D8B22800A",
    "host": {
      "os": "",
      "cpu": "",
      "cpuCount": 24,
      "osVersion": "",
      "memorySize": "15.7303391G"
    },
    "event": "bootstraped",
    "version": "1.3.0.0",
    "instance": {
      "port": 2881,
      "hostHash": "12CA17B49AF2289436F303E0166030A21E525D266E209267433801A8FD4071A0",
      "createTimestamp": 1793435290146393
    },
    "reporter": "server",
    "resource": {
      "cpuCount": 24,
      "logDiskSize": "0G",
      "memoryLimit": "2G",
      "dataFileSize": "0.03125G"
    },
    "telemetryVersion": 1
  },
  "component": "seekdb"
}
```
