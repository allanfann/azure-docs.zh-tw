---
title: 翻譯自訂 - 翻譯工具文字 API
titlesuffix: Azure Cognitive Services
description: 以慣用的術語和樣式，使用 Microsoft Translator Hub 建置您自己的電腦翻譯系統。
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: a04f6fab26a47b87bf55f1714522cad648dc5fad
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205914"
---
# <a name="customize-your-text-translations"></a>自訂文字翻譯

Microsoft 自訂翻譯工具是 Microsoft 翻譯工具服務的功能，可讓使用者在使用翻譯工具文字 API (僅限第 3 版) 翻譯文字時，自訂 Microsoft 翻譯工具的進階類神經機器翻譯。

此功能在搭配使用[認知服務語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/)時，也可用來自訂語音翻譯。

## <a name="custom-translator"></a>自訂翻譯工具

利用自訂翻譯工具，您可以建置類神經翻譯系統，以了解您自己的企業和產業中使用的術語。 然後，自訂的翻譯系統會整合到現有的應用程式、工作流程和網站。

### <a name="how-does-it-work"></a>其運作方式為何?

使用您之前翻譯的文件 (傳單、網頁、文件等) 來建置反映特定領域術語和樣式的翻譯系統，比一般翻譯系統更好。 使用者可以上傳 TMX、XLIFF、TXT、DOCX 和 XLSX 文件。  

系統也接受在文件層級上為平行處理、但在句子層級上尚未對齊的資料。 若使用者可以存取使用多種語言、但位於在不同文件中的相同內容版本，自訂翻譯工具就能夠自動比對文件之間的句子。  系統還可以使用其中一或二種語言的單語資料，補充平行訓練資料來改善翻譯。

然後，您可以利用 category 參數，透過定期呼叫 Microsoft 翻譯工具文字 API 來使用自訂系統。

假設有適當的訓練資料類型和數量，透過使用自訂翻譯工具預期翻譯品質取得介於 5 到 10 之間或甚至更多的 BLEU 點數並不罕見。

如需根據可用資料之各種自訂層級的詳細資料，請參閱[自訂翻譯工具使用者指南](https://aka.ms/CustomTranslatorDocs)。


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> 將於 2019 5 月 17 日淘汰舊版的 Microsoft Translator 中樞。 [檢視重要的移轉資訊和日期](https://www.microsoft.com/translator/business/hub/)。  

## <a name="custom-translator-versus-hub"></a>自訂翻譯工具與中樞

|   | **中心** | **自訂翻譯工具**|
|:-----|:----:|:----:|
|自訂功能狀態   | 正式運作  | 正式運作 |
| 文字 API 版本  | 僅限第 2 版   | 僅限第 3 版 |
| SMT 自訂 | 有   | 無 |
| NMT 自訂 | 無    | 有 |
| 新的統一語音服務自訂 | 無    | 有 |
| [不追蹤](https://www.aka.ms/notrace) | 有  | 有 |

## <a name="collaborative-translations-framework"></a>共同作業翻譯架構

> [!NOTE]
> 自 2018 年 2 月 1 起，AddTranslation() 和 AddTranslationArray() 不再適用於翻譯工具文字 API 2.0 版。 這些方法將會失敗，不會寫入任何內容。 翻譯工具文字 API 3.0 版不支援這些方法。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用自訂翻譯工具設定自訂的語言系統](https://aka.ms/CustomTranslatorDocs)
