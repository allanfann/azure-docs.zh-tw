---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097652"
---
建立可用性群組接聽程式之後，可能需要調整接聽程式資源的 RegisterAllProvidersIP 和 HostRecordTTL 叢集參數。 這些參數可縮短容錯移轉之後的重新連線時間，並可能防止連線逾時。 如需這些參數的詳細資訊以及範例程式碼，請參閱[建立或設定可用性群組接聽程式](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。

<!-- Update_Description: update meta properties -->