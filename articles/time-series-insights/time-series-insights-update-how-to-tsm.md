---
title: Azure 時間序列深入解析預覽版中的資料模型 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽版中的資料模型。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 1c8886cada80c02e99782159099aa626da35fc50
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466864"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽版中的資料模型

本文件描述如何透過 Azure 時間序列深入解析預覽版使用時間序列模型。 它詳細說明數個常見資料案例。

若要深入了解如何使用該更新，請參閱 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。

## <a name="types"></a>類型

### <a name="create-a-single-type"></a>建立單一類型

1. 移至時間序列模型選取器面板，然後從功能表選取 [Types] \(類型\)。 摺疊面板以將焦點放在時間序列模型類型。

    [![建立單一的類型](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. 選取 [新增] 。
1. 輸入與類型有關的所有詳細資料，並選取 [建立]。 此動作會在環境中建立類型。

    [![加入類型](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>大量上傳一或多個類型

1. 選取 [上傳 JSON]。
1. 選取包含類型承載的檔案。
1. 選取 [上傳] 。

    [![上傳 JSON](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>編輯單一類型

1. 選取類型，然後選取 [編輯]。 
1. 進行必要的變更，然後選取 [儲存]。

    [![編輯類型](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>刪除類型

1. 選取類型，然後選取 [刪除]。
1. 如果沒有執行個體與該類型相關聯，系統就會刪除它。

    [![刪除類型](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>階層

### <a name="create-a-single-hierarchy"></a>建立單一階層

1. 移至時間序列模型選取器面板，然後從功能表選取 [Hierarchies] \(階層\)。 摺疊面板以將焦點放在時間序列模型階層。

    [![選取 [階層]](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. 選取 [新增] 。

    [![在其中加入階層](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. 在右窗格中選取 [新增層級]。

    [![新增一個層級](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. 請輸入階層的詳細資訊，並選取 [建立]。

    [![建立一個層級](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>大量上傳一或多個階層

1. 選取 [上傳 JSON]。
1. 選取包含階層承載的檔案。
1. 選取 [上傳] 。

    [![大量上傳階層](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>編輯單一階層

1. 選取階層，然後選取 [編輯]。
1. 進行必要的變更，然後選取 [儲存]。

    [![編輯單一階層](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>刪除階層

1. 選取階層，然後選取 [刪除]。 
1. 如果沒有執行個體與該階層相關聯，系統就會刪除它。

    [![刪除階層](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>執行個體

### <a name="create-a-single-instance"></a>建立單一執行個體

1. 移至時間序列模型選取器面板，然後從功能表選取 [Instances] \(執行個體\)。 摺疊面板以將焦點放在時間序列模型執行個體。

    [![建立單一執行個體](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. 選取 [新增] 。

    [![新增執行個體](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. 輸入執行個體詳細資料，選取類型和階層關聯，然後選取 [建立]。

### <a name="bulk-upload-one-or-more-instances"></a>大量上傳一或多個執行個體

1. 選取 [上傳 JSON]。
1. 選取包含執行個體承載的檔案。

    [![大量上傳的一或多個執行個體](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. 選取 [上傳] 。

### <a name="edit-a-single-instance"></a>編輯單一執行個體

1. 選取執行個體，然後選取 [編輯]。 
1. 進行必要的變更，然後選取 [儲存]。

    [![編輯單一執行個體](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 如需有關時間序列模型的詳細資訊，請參閱[資料模型](./time-series-insights-update-tsm.md)。

- 若要深入了解預覽版，請參閱[在 Azure 時間序列深入解析預覽版總管中將資料視覺化](./time-series-insights-update-explorer.md)。

- 若要了解支援的 JSON 塑形，請參閱[支援的 JSON 塑形](./time-series-insights-send-events.md#json)。
