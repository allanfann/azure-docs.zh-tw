---
title: 內建資料擷取、自然語言、影像處理功能 - Azure 搜尋服務
description: 資料擷取、自然語言、影像處理認知等技能會在 Azure 搜尋服務管線中對原始內容新增語意和結構。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7925f3aef4123fddd3a96c6e62971b881ae4cbc3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021867"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>內容擴充的預先定義技能 (Azure 搜尋服務)

在本文中，您會了解 Azure 搜尋服務所提供的認知技能。 *認知技能*是一種以某種方式轉換內容的作業。 它通常是一種擷取資料或推斷結構的元件，因此能提高我們對輸入資料的了解。 輸出幾乎都是以文字為基礎的。 *技能集*是定義擴充管線的技能集合。 

> [!NOTE]
> 當您展開範圍增加處理的頻率加入更多的文件，或新增更多的 AI 演算法，您必須[附加的可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 搜尋服務的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 執行內建的技能收費現有[認知服務付移價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。 映像擷取定價會說明[Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。


## <a name="predefined-skills"></a>預先定義的技能

有數種技能所取用或產生的內容都很具彈性。 一般而言，大部分的技能乃是依據預先定型的模型，這表示您無法使用自己的定型資料來定型模型。 下表列舉並說明 Microsoft 所提供的技能。 

| 技能 | 描述 |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | 這項技能使用預先定型的模型，根據字詞位置、語言規則、與其他字詞的鄰近程度，以及字詞在來源資料中是否尋常來偵測重要詞彙。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 這項技能使用預先定型的模型，偵測所使用的語言 (每個文件一個語言識別碼)。 當相同的文字區段內使用多種語言時，輸出是主要使用語言的 LCID。|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 將一組欄位中的文字合併成單一欄位。  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | 這項技能會使用預先定型的模型，為一組固定的類別 (人員、位置、組織、電子郵件、URL、日期時間欄位) 建立實體。 |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | 這項技能使用預先定型的模型，為每一筆記錄評定正分或負分的人氣。 分數介於 0 到 1 之間。 當無法偵測到人氣時的 Null 案例，以及對於視為中性的文字，兩者都會發生中性的分數。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 將文字分成多個頁面，讓您能夠以遞增方式來擴充或增加內容。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 這項技能使用影像偵測演算法，以識別影像的內容並產生文字描述。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光學字元辨識。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 將輸出對應到複雜類型 (多部分的資料類型，可用於全名、多行地址，或姓氏與個人識別碼的組合)。 |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 可藉由將 HTTP 呼叫發到自訂 Web API 來擴充認知搜尋管線 |


如需建立[自訂技能](cognitive-search-custom-skill-web-api.md)的指引，請參閱[如何定義自訂介面](cognitive-search-custom-skill-interface.md)和[範例：建立自訂技能](cognitive-search-create-custom-skill-example.md)。

## <a name="see-also"></a>請參閱

+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [自訂技能介面定義](cognitive-search-custom-skill-interface.md)
+ [教學課程：擴充認知搜尋的編製索引](cognitive-search-tutorial-blob.md)
