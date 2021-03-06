---
title: 使用 Visual Studio 搭配 .NET 和 C# 查詢 Azure SQL Database | Microsoft Docs
description: 使用 Visual Studio 來建立連線到 Azure SQL Database 的 C# 應用程式，並使用 Transact-SQL 陳述式來查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7c444235ed7aa641fe218b4d1667e661ff05d77e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443675"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>快速入門：在 Visual Studio 使用 .NET 和 C# 連線和查詢 Azure SQL 資料庫

本快速入門示範如何在 Visual Studio 使用 [.NET Framework](https://www.microsoft.com/net/) 和 C# 程式碼，以 Transact-SQL 陳述式來查詢 Azure SQL 資料庫。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- Azure SQL 資料庫。 您可以使用其中一個快速入門，在 Azure SQL Database 中建立資料庫並加以設定：

  || 單一資料庫 | 受控執行個體 |
  |:--- |:--- |:---|
  | 建立| [入口網站](sql-database-single-database-get-started.md) | [入口網站](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 設定 | [伺服器層級 IP 防火牆規則](sql-database-server-level-firewall-rule.md)| [VM 的連線能力](sql-database-managed-instance-configure-vm.md)|
  |||[現場的連線能力](sql-database-managed-instance-configure-p2s.md)
  |載入資料|每個快速入門載入的 Adventure Works|[還原 Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 中的 [BACPAC](sql-database-import.md) 檔案還原或匯入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中已撰寫的指令碼會使用 Adventure Works 資料庫。 對於受控執行個體，您必須將 Adventure Works 資料庫匯入執行個體資料庫中，或將本文中的指令碼修改為使用 Wide World Importers 資料庫。

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community、Professional 或 Enterprise 版。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對單一資料庫檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機] 旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。 

## <a name="create-code-to-query-the-sql-database"></a>建立程式碼以查詢 SQL Database

1. 在 Visual Studio 中，選取 [檔案]  >  [新增]  >  [專案]。 
   
1. 在 [新增專案] 對話方塊中，選取 [Visual C#]，然後選取 [主控台應用程式 (.NET Framework)]。
   
1. 輸入 *sqltest* 作為專案名稱，然後選取 [確定]。 隨即建立新專案。 
   
1. 選取 [專案] > [管理 NuGet 套件]。 
   
1. 在 [NuGet 套件管理員] 中，選取 [瀏覽] 索引標籤，然後搜尋並選取 **System.Data.SqlClient**。
   
1. 在 **System.Data.SqlClient** 頁面上，選取 [安裝]。 
   - 出現提示時，選取 [確定] 以繼續安裝。 
   - 如果 [接受授權] 視窗出現時，選取 [我接受]。
   
1. 當安裝完成時，您可以關閉 [NuGet 套件管理員]。 
   
1. 在程式碼編輯器中，以下列程式碼取代 **Program.cs** 內容。 請將 `<server>`、`<username>`、`<password>` 及 `<database>` 替換為您的值。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>執行程式碼

1. 若要執行應用程式，請選取 [偵錯] > [開始偵錯]，或在工具列上選取 [開始]，或按下 **F5**。
1. 請確認會傳回資料庫的前 20 個「類別/產品」資料列，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- 了解如何在 Windows/Linux/macOS 中[使用 .NET Core 來連線及查詢 Azure SQL 資料庫](sql-database-connect-query-dotnet-core.md)。  
- 了解 [使用命令列以開始使用在 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)。
- 深入了解如何[使用 SSMS 設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)或[使用 .NET 設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database-csharp.md)。
- 如需 .NET 的詳細資訊，請參閱 [.NET 文件](https://docs.microsoft.com/dotnet/)。
- 重試邏輯範例：[使用 ADO.NET 復原連線 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

