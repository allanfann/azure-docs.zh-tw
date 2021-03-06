---
title: SQL 数据库审核日志格式 | Microsoft Docs
description: 了解 SQL 数据库审核日志的构建方式。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417383"
---
# <a name="sql-database-audit-log-format"></a>SQL 数据库审核日志格式

[Azure SQL Database 稽核](sql-database-auditing.md)會追蹤資料庫事件並將寫入的稽核記錄的 Azure 儲存體帳戶中，或將它們傳送到事件中樞或 Log Analytics 中進行下游處理和分析。

## <a name="naming-conventions"></a>命名约定

### <a name="blob-audit"></a>Blob 审核

Blob 存储中存储的审核日志存储在 Azure 存储帐户中名为 `sqldbauditlogs` 的容器内。 该容器中的目录层次结构采用 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` 格式。 Blob 文件名的格式为 `<CreationTime>_<FileNumberInSession>.xel`，其中，`CreationTime` 采用 UTC `hh_mm_ss_ms` 格式，`FileNumberInSession` 是运行的索引（如果会话日志跨多个 Blob 文件）。

例如，对于 `Server1` 上的数据库 `Database1`，下面是可能的有效路径：

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>事件中樞

稽核事件會寫入至已定義稽核的設定期間，所擷取的主體中的命名空間和事件中樞[Apache Avro](https://avro.apache.org/)事件並使用 JSON 格式化以 utf-8 編碼方式儲存。 若要讀取稽核記錄，您可以使用 [Avro Tools](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) 或類似工具來處理這種格式。

### <a name="log-analytics"></a>Log Analytics

稽核事件會寫入至稽核的設定，期間所定義的 Log Analytics 工作區`AzureDiagnostics`類別的資料表`SQLSecurityAuditEvents`。 如需 Log Analytics 搜尋語言和命令的其他實用資訊，請參閱 [Log Analytics 搜尋參考](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)。

## <a id="subheading-1"></a>审核日志字段

| 名称 (Blob) | 名稱 (事件中樞/Log Analytics) | 描述 | Blob 類型 | 事件中樞/Log Analytics 類型 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 操作的 ID | varchar(4) | string |
| action_name | action_name_s | 操作的名称 | N/A | string |
| additional_information | additional_information_s | 有关事件的任何附加信息，以 XML 形式存储 | nvarchar(4000) | string |
| affected_rows | affected_rows_d | 查询影响的行数 | bigint | int |
| application_name | application_name_s| 客户端应用程序的名称 | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | 始终为 1 | int | int |
| class_type | class_type_s | 发生审核的可审核实体的类型 | varchar(2) | string |
| class_type_desc | class_type_description_s | 发生审核的可审核实体的说明 | N/A | string |
| client_ip | client_ip_s | 客户端应用程序的源 IP | nvarchar(128) | string |
| connection_id | N/A | 服务器中的连接的 ID | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 受审核查询根据数据库中分类的列返回的信息类型和敏感度标签。 详细了解 [Azure SQL 数据库数据发现和分类](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | 在其中执行操作的数据库上下文 | sysname | string |
| database_principal_id | database_principal_id_d | 在其中执行操作的数据库用户上下文的 ID | int | int |
| database_principal_name | database_principal_name_s | 在其中执行操作的数据库用户上下文的名称 | sysname | string |
| duration_milliseconds | duration_milliseconds_d | 查询执行持续时间，以毫秒为单位 | bigint | int |
| event_time | event_time_t | 激发可审核操作的日期和时间 | datetime2 | datetime |
| host_name | N/A | 客户端主机名 | string | N/A |
| is_column_permission | is_column_permission_s | 指示是否为列级权限的标志。 1 = true，0 = false | bit | string |
| N/A | is_server_level_audit_s | 指示此项审核是否在服务器级别发生的标志 | N/A | string |
| object_id | object_id_d | 发生审核的实体的 ID。 包括：服务器对象、数据库、数据库对象和架构对象。 如果实体是服务器本身，或者审核不是在对象级别执行的，则值为 0 | int | int |
| object_name | object_name_s | 发生审核的实体的名称。 包括：服务器对象、数据库、数据库对象和架构对象。 如果实体是服务器本身，或者审核不是在对象级别执行的，则值为 0 | sysname | string |
| permission_bitmask | permission_bitmask_s | 在适用的情况下，显示已授予、已拒绝或已撤销的权限 | varbinary(16) | string |
| response_rows | response_rows_d | 在结果集中返回的行数 | bigint | int |
| schema_name | schema_name_s | 在其中执行操作的架构上下文。 如果审核在架构外部发生，则值为 NULL | sysname | string |
| N/A | securable_class_type_s | 映射到正在审核的 class_type 的安全对象 | N/A | string |
| sequence_group_id | sequence_group_id_g | 唯一識別碼 | varbinary | GUID |
| sequence_number | sequence_number_d | 跟踪大小过大、以致无法装入审核写入缓冲区的单个审核记录中的记录序列 | int | int |
| server_instance_name | server_instance_name_s | 发生审核的服务器实例的名称 | sysname | string |
| server_principal_id | server_principal_id_d | 在其中执行操作的登录上下文的 ID | int | int |
| server_principal_name | server_principal_name_s | 当前登录名 | sysname | string |
| server_principal_sid | server_principal_sid_s | 当前登录名 SID | varbinary | string |
| session_id | session_id_d | 发生事件的会话的 ID | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 会话的服务器主体 | sysname | string |
| 陳述式 | statement_s | 执行的 T-SQL 语句（如果有） | nvarchar(4000) | string |
| 成功 | succeeded_s | 指示触发事件的操作是否成功。 对于除登录和批处理以外的事件，此字段仅报告权限检查是成功还是失败，而不会报告操作结果。 1 = 成功，0 = 失败 | bit | string |
| target_database_principal_id | target_database_principal_id_d | 执行 GRANT/DENY/REVOKE 操作的数据库主体。 如果不适用，则值为 0 | int | int |
| target_database_principal_name | target_database_principal_name_s | 操作的目标用户。 如果不适用，则值为 NULL | string | string |
| target_server_principal_id | target_server_principal_id_d | 执行 GRANT/DENY/REVOKE 操作的服务器主体。 如果不适用，则返回 0 | int | int |
| target_server_principal_name | target_server_principal_name_s | 操作的目标登录名。 如果不适用，则值为 NULL | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | 目标登录名的 SID。 如果不适用，则值为 NULL | varbinary | string |
| transaction_id | transaction_id_d | 仅适用于 SQL Server（2016 和更高版本）- 对于 Azure SQL 数据库，值为 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | 用户定义的事件 ID，作为参数传递给 sp_audit_write。 对于系统事件，值为 NULL（默认值）；对于用户定义的事件，值为非零值。 有关详细信息，请参阅 [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | 用户定义的信息，作为参数传递给 sp_audit_write。 对于系统事件，值为 NULL（默认值）；对于用户定义的事件，值为非零值。 有关详细信息，请参阅 [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>後續步驟

详细了解 [Azure SQL 数据库审核](sql-database-auditing.md)。