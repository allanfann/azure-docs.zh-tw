---
title: 使用 Azure Data Factory 以累加方式複製資料 | Microsoft Docs
description: 這些教學課程會說明，如何以累加方式將資料從來源資料存放區複製到目的地資料存放區。 第一個教學課程會從一個資料表複製資料。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: cb75e943416c227730589ab5e7feeb7b8ba5e245
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957917"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>以累加方式將資料從來源資料存放區載入到目的地資料存放區

在資料整合解決方案中，我們經常會見到在初次載入完整資料之後，再以累加方式 (或差異) 載入資料的情形。 本節的教學課程會說明各種使用 Azure Data Factory 來累加載入資料的方式。

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>使用水位線從資料庫載入差異資料
在此案例中，您會在來源資料庫中定義水位線。 水位線這個資料行上會有上次更新時間戳記或遞增索引鍵。 差異載入解決方案會載入舊水位線和新水位線之間有所變更的資料。 下圖會說明這種方法的工作流程： 

![使用水位線的工作流程](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

如需逐步指示，請參閱下列教學課程： 

- [以累加方式將資料從 Azure SQL Database 中的一個資料表複製到 Azure Blob 儲存體](tutorial-incremental-copy-powershell.md)
- [以累加方式將內部部署 SQL Server 中多個資料表的資料複製到 Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>使用變更追蹤技術從 SQL DB 載入差異資料
變更追蹤技術是 SQL Server 和 Azure SQL Database 中的輕量型解決方案，可為應用程式提供有效率的變更追蹤機制。 它可讓應用程式輕鬆地識別已插入、更新或刪除的資料。 

下圖會說明這種方法的工作流程：

![使用變更追蹤的工作流程](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

如需逐步指示，請參閱下列教學課程： <br/>
[使用變更追蹤技術，以累加方式將資料從 Azure SQL Database 複製到 Azure Blob 儲存體](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>只能藉由使用 LastModifiedDate 載入新的和變更的檔案
您可以先取得檔案的中繼資料 (LastModifiedDate)，且僅將新增和變更的檔案複製到目的地存放區。

如需逐步指示，請參閱下列教學課程： <br/>
[根據 LastModifiedDate，以累加方式將新增和變更的檔案從 Azure Blob 儲存體複製到 Azure Blob 儲存體](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>只能藉由使用時間分割的資料夾或檔案的名稱，載入新檔案。
您只能複製新檔案，其中的檔案或資料夾已經過時間分割，並以時間片段資訊作為檔案或資料夾名稱的一部分 (例如 /yyyy/mm/dd/file.csv)。 

如需逐步指示，請參閱下列教學課程： <br/>
[根據時間分割的資料夾或檔案名稱，以累加方式將新檔案從 Azure Blob 儲存體複製到 Azure Blob 儲存體](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>後續步驟
前進到下列教學課程： 

> [!div class="nextstepaction"]
>[以累加方式將資料從 Azure SQL Database 中的一個資料表複製到 Azure Blob 儲存體](tutorial-incremental-copy-powershell.md)
