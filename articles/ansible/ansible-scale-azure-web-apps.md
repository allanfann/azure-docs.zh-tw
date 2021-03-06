---
title: 教學課程 - 使用 Ansible 調整 Azure App Service 中的應用程式 | Microsoft Docs
description: 了解如何相應增加 Azure App Service 中的應用程式
keywords: ansible, azure, devops, bash, 劇本, Azure App Service, Web App, 調整規模, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d63708cd87afa426f2712da6d0fcb11c84590798
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230955"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>教學課程：使用 Ansible 調整 Azure App Service 中的應用程式

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 取得現有 App Service 方案的資訊
> * 將 App Service 方案相應增加為具有三個背景工作的 S2

## <a name="prerequisites"></a>必要條件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service 應用程式** - 如果您沒有 Azure App Service 應用程式，請[使用 Ansible 在 Azure App Service 中設定應用程式](ansible-create-configure-azure-web-apps.md)。

## <a name="scale-up-an-app"></a>相應增加應用程式

調整有兩個工作流程：*相應增加*和*相應放大*。

**相應增加：** 相應增加的實質意義，是取得更多資源。 這些資源包括 CPU、記憶體、磁碟空間、VM 等。 您可以藉由變更應用程式所屬 App Service 方案的定價層，將應用程式相應增加。 
**相應放大：** 相應放大的實質意義，是增加執行應用程式的 VM 執行個體數目。 視 App Service 方案的定價層而定，您最多可以相應放大至 20 個執行個體。 [自動調整](/azure/azure-monitor/platform/autoscale-get-started)可讓您根據預先定義的規則和排程自動調整執行個體計數。

本節中的劇本程式碼會定義下列作業︰

* 取得現有 App Service 方案的資訊
* 將 App Service 方案更新為具有三個背景工作角色的 S2

請下列腳本儲存為 `webapp_scaleup.yml`：

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook webapp_scaleup.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [Ansible on Azure](/azure/ansible/)