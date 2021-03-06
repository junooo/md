# IP 管理

## 查看 IP 费用

查看某 IP 实例创建至今产生的所有费用。

### 请求 URL

`GET https://open.c.163.com/api/v1/ips/{id}/cost`

### 请求示例

```http
GET /api/v1/ips/163/cost HTTP/1.1
Host: open.c.163.com
Authorization: Token 93cb02be6a83447a8dfd83221e8d4a96
Content-Type: application/json
```

### 请求参数


| 参数 |  类型  | 是否必填 |                       描述                       | 示例值 |
|------|--------|----------|--------------------------------------------------|--------|
| id   | String | 是       | IP 的唯一标识符（[获取 IP 列表](http://support.c.163.com/md.html#!平台服务/IP 管理/API 手册/获取 IP 列表.md)） |    163 |


### 响应示例

```json
{
  "id": "163",
  "cost": 1.26
}
```

### 响应参数

| 参数 |  类型  |       描述      | 示例值 |
|------|--------|-----------------|--------|
| id   | String | IP 的唯一标识符 |    163 |
| cost | Number | 费用，单位：元  |   1.26 |
