---
title: 使用 Azure 备份与 PowerShell 来备份和恢复 Azure VM
description: 介绍如何使用 Azure 备份与 PowerShell 来备份和恢复 Azure VM
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 10af40a1f671d5871204ff465395c8c3619671f7
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232499"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>使用 PowerShell 备份和恢复 Azure VM

本文介绍如何使用 PowerShell cmdlet 在 [Azure 备份](backup-overview.md)恢复服务保管库中备份和还原 Azure VM。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立復原服務保存庫並設定保存庫內容。
> * 定義備份原則
> * 套用備份原則以保護多部虛擬機器
> * 觸發受保護虛擬機器的隨選備份作業。在備份 (或保護) 虛擬機器之前，您必須先完成[先決條件](backup-azure-arm-vms-prepare.md)來備妥保護 VM 的環境。

## <a name="before-you-start"></a>開始之前

- [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
- [檢閱](backup-architecture.md#architecture-direct-backup-of-azure-vms)Azure VM 備份的架構[深入了解](backup-azure-vms-introduction.md)備份程序，以及[檢閱](backup-support-matrix-iaas.md)支援、 限制和必要條件。
- 查看恢复服务的 PowerShell 对象层次结构。

## <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下图汇总了对象层次结构。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 库中的 **Az.RecoveryServices** [cmdlet 参考](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0)。

## <a name="set-up-and-register"></a>设置和注册

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

開始：

1. [下载最新版本的 PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. 輸入下列命令，以找到可用的 Azure 備份 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    將顯示 Azure備份、Azure Site Recovery 與復原服務保存庫的別名與 Cmdlet。 下圖是您將看到的範例。 它不是 Cmdlet 的完整清單。

    ![復原服務清單](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. 使用 **Connect-AzAccount** 登入您的 Azure 帳戶。 此 Cmdlet 會開啟網頁，提示您輸入帳戶認證：

    * 或者，您可以使用 **-Credential** 參數，以參數形式將您的帳戶認證加入 **Connect-AzAccount** Cmdlet。
    * 如果您是代表租用戶工作的 CSP 合作夥伴，請使用客戶的 tenantID 或租用戶主要網域名稱將客戶指定為租用戶。 例如：**Connect-AzAccount -Tenant "fabrikam.com"**

4. 由於一個帳戶可以有多個訂用帳戶，因此您必須將要使用的訂用帳戶與帳戶建立關聯：

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. 首次使用 Azure 备份时，必须使用 **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 您可以使用下列命令，確認提供者已成功註冊：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    在命令輸出中，**RegistrationState** 應該變更為 **Registered**。 如果未按此更改，只需再次运行 **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet 即可。


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

下列步驟將引導您完成建立復原服務保存庫。 恢复服务保管库不同于备份保管库。

1. 復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 可以使用现有的资源组，也可以使用 **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet 创建资源组。 建立資源群組時，請指定資源群組的名稱與位置。  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. 使用 [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) Cmdlet 來建立復原服務保存庫。 請務必為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 指定要使用的儲存體備援類型；您可以使用[本地備援儲存體 (LRS)](../storage/common/storage-redundancy-lrs.md) 或[異地備援儲存體 (GRS)](../storage/common/storage-redundancy-grs.md)。 下列範例顯示 testvault 的 -BackupStorageRedundancy 選項設為 GeoRedundant。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。 基於這個理由，將備份復原服務保存庫物件儲存在變數中會是方便的做法。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)：

```powershell
Get-AzRecoveryServicesVault
```

輸出會類似下列的範例，請注意，會提供相關聯的 ResourceGroupName 與位置。

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>備份 Azure VM

使用復原服務保存庫來保護您的虛擬機器。 在您套用保護之前，請設定保存庫內容 (保存庫中所保護的資料類型)，並確認保護原則。 保护策略是指备份作业运行时以及每个备份快照的保留时长的计划。

### <a name="set-vault-context"></a>設定保存庫內容

在 VM 上启用保护之前，请使用 [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) 来设置保管库上下文。 保存庫內容設定之後就會套用至所有後續的 Cmdlet。 下列範例會設定保存庫 *testvault* 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>修改存储复制设置

使用[組 AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)設為 LRS/GRS 的儲存體複寫設定保存庫的命令

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> 只有當保存庫中沒有受保護的備份項目時，才可以修改儲存體備援。

### <a name="create-a-protection-policy"></a>建立保護原則

當您建立復原服務保存庫時，它會隨附預設的保護和保留原則。 預設保護原則會在每天的指定時間觸發備份作業。 預設保留原則會將每日復原點保留 30 天。 您可以使用預設原則來快速地保護 VM，並在之後編輯原則的各種詳細資料。

若要查看保管库中可用的保护策略，请使用 **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)。 您可以使用此 Cmdlet 來取得特定的原則，或檢視與工作負載類型相關聯的原則。 下列範例會取得工作負載類型 AzureVM 的原則。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

輸出類似於下列範例：

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell 中 BackupTime 欄位的時區是 UTC。 不過，當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

備份保護原則至少與一個保留原則相關聯。 保留策略定义了在将恢复点删除之前将其保留多长时间。

- 使用 [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) 檢視預設保留原則。
- 同樣地，您可以使用 [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) 取得預設排程原則。
- [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) Cmdlet 會建立 PowerShell 物件來保存備份原則資訊。
- 计划和保留策略对象将用作 New-AzRecoveryServicesBackupProtectionPolicy cmdlet 的输入。

默认情况下，会在“计划策略对象”中定义开始时间。 请使用以下示例将开始时间更改为所需的开始时间。 所需的开始时间也应采用 UTC 格式。 以下示例假设在进行每日备份时，所需的开始时间为 UTC 时间凌晨 1:00。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

下列範例會將排程原則和保留原則儲存在變數中。 這個範例在建立保護原則 *NewPolicy* 時會使用這些變數來定義參數。

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出類似於下列範例：

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>啟用保護

在定義保護原則之後，您仍然必須對項目啟用此原則。 请使用 [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 来启用保护。 啟用保護需要兩個物件：項目和原則。 一旦原則與保存庫相關聯，備份工作流程將依照原則排程定義的時間觸發。

下列範例會使用原則 NewPolicy 來對項目 V2VM 啟用保護。 這些範例根據 VM 是否加密以及加密的類型而有所不同。

在**非加密 Resource Manager VM** 上啟用保護：

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

若要在加密 VM（使用 BEK 和 KEK 加密）上启用保护，必须向 Azure 备份服务授予权限来读取密钥保管库中的密钥和机密。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

若要在**加密的 VM 上 (只使用 BEK 加密)** 啟用保護，您必須提供權限讓 Azure 備份服務讀取金鑰保存庫中的密碼。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> 如果您使用 Azure Government 雲端，然後使用值 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 參數 ServicePrincipalName 中[組 AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet。
>

## <a name="monitoring-a-backup-job"></a>監視備份工作

您不必透過 Azure 入口網站就可以監視長時間執行的作業，例如備份作業。 若要获取正在进行的作业的状态，请使用 [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet。 此 Cmdlet 會取得特定保存庫 (在保存庫內容中指定) 的備份作業。 下列範例會以陣列形式取得進行中作業的狀態，並將狀態儲存在 $joblist 變數中。

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

輸出類似於下列範例：

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

与其使用额外的不必要的代码来轮询这些作业的完成情况，不如使用 [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet。 此 Cmdlet 會暫停執行，直到工作完成，或達到指定的逾時值為止。

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>管理 Azure VM 備份

### <a name="modify-a-protection-policy"></a>修改保護原則

若要修改保护策略，请使用 [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 修改 SchedulePolicy 或 RetentionPolicy 对象。

#### <a name="modifying-scheduled-time"></a>修改排定的時間

當您建立保護原則時，它會指派預設的開始時間。 下列範例示範如何修改保護原則的開始時間。

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>修改的保留期

下列範例會將復原點保留變更為 365 天。

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>配置即时还原快照保留期

> [!NOTE]
> 从 Az PS 版本 1.6.0 开始，用户可以使用 Powershell 在策略中更新即时还原快照保留期

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

默认值为 2，用户可以将值设置为 1 到 5。 每周备份策略的保留期设置为 5，不能更改。

### <a name="trigger-a-backup"></a>觸發備份

请使用 [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) 来触发备份作业。 如果是初始備份，則會是完整備份。 後續的備份會採用增量複本。 下列範例會在 VM 備份會保留 60 天。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

輸出類似於下列範例：

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell 中 StartTime 和 EndTime 欄位的時區是 UTC。 不過，當時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

### <a name="change-policy-for-backup-items"></a>變更備份項目原則

使用者可以修改現有的原則，或從 [policy1] 中變更的原則備份的項目，為 Policy2。 若要切換原則備份的項目，只要擷取相關的原則和備份項目，並使用[啟用 AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)命令與備份的項目，做為參數。

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

此命令會等到完成設定備份，並傳回下列輸出。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>停止保護

#### <a name="retain-data"></a>保留資料

如果使用者想要停止保護，則可以使用[停用 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS 指令程式。 這將會停止排定的備份，但直到備份的資料現在會永遠保留。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>刪除備份資料

若要完全移除備份的資料儲存在保存庫，只要新增 '-RemoveRecoveryPoints' 旗標/切換到['disable' 保護命令](#retain-data)。

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>還原 Azure VM

使用 Azure 入口網站還原 VM 和使用 PowerShell 還原 VM 之間有一個重要差異。 使用 PowerShell 時，建立磁碟和復原點組態資訊之後，還原作業即完成。 還原作業不會建立虛擬機器。 若要從磁碟建立虛擬機器，請參閱[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)一節。 如果您不想還原整個 VM，但想要從 Azure VM 備份還原或復原幾個檔案，請參閱[檔案復原](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)一節。

> [!Tip]
> 還原作業不會建立虛擬機器。
>
>

下圖顯示從 RecoveryServicesVault 到 BackupRecoveryPoint 的物件階層。

![顯示 BackupContainer 的復原服務物件階層](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

若要還原備份資料，請識別已備份的項目和保存時間點資料的復原點。 请使用 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 将数据从保管库还原到帐户。

還原 Azure VM 的基本步驟如下︰

* 選取 VM。
* 選擇復原點。
* 還原磁碟。
* 從預存的磁碟建立 VM。

### <a name="select-the-vm"></a>選取 VM

若要取得可識別正確備份項目的 PowerShell 物件，請從保存庫中的容器開始，向下深入物件階層。 若要选择代表 VM 的容器，请使用 [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet，然后通过管道将其传递给 [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>選擇復原點

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) Cmdlet 來列出備份項目的所有復原點。 接下來選擇要還原的復原點。 如果您不確定要使用哪一個復原點，在清單中選擇最近的 RecoveryPointType = AppConsistent 點是好的做法。

在下列指令碼中，變數 **$rp** 是選取的備份項目在過去七天的復原點陣列。 陣列是以相反時間順序排序，最新復原點位於索引 0。 使用標準 PowerShell 陣列索引來挑選復原點。 在範例中，$rp[0] 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

輸出類似於下列範例：

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>還原磁碟

请使用 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) cmdlet 将备份项的数据和配置还原到某个恢复点。 在您識別復原點之後，請使用它作為 **-RecoveryPoint** 參數的值。 在上面的範例中，**$rp[0]** 是要使用的復原點。 在接下來的範例程式碼中，**$rp[0]** 是要用來還原磁碟的復原點。

還原磁碟和設定資訊：

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>還原受控磁碟

> [!NOTE]
> 如果備份的 VM 含有受控磁碟，而您想要將它們還原成受控磁碟，我們從 Azure PowerShell RM 模組 v 6.7.0 開始便已導入該功能 。
>
>

請提供額外的參數 **TargetResourceGroupName**以指定將作為受控磁碟還原目的地的 RG。 

> [!NOTE]
> 強烈建議使用 **TargetResourceGroupName** 參數來還原受控磁碟，因為這會導致效能顯著改善。 此外，從 Azure Powershell Az 模組 1.0 起，這是還原受控磁碟時的必要參數。
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** 檔案將會還原至儲存體帳戶，而受控磁碟則會還原至指定的目標 RG。

輸出類似於下列範例：

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

请使用 [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet 等待还原作业完成。

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

还原作业完成后，请使用 [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet 获取还原操作的详细信息。 JobDetails 屬性具有重建 VM 所需的資訊。

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

還原磁碟之後，請繼續下一節來建立 VM。

## <a name="create-a-vm-from-restored-disks"></a>從還原的磁碟建立 VM

在您還原磁碟之後，使用下列步驟從磁碟建立及設定虛擬機器。

> [!NOTE]
> 若要從預存的磁碟建立加密的 VM，您的 Azure 角色必須具備可執行 **Microsoft.KeyVault/vaults/deploy/action** 動作的權限。 如果您的角色並沒有此權限，請使用此動作來建立自訂角色。 如需詳細資訊，請參閱 [Azure RBAC 中的自訂角色](../role-based-access-control/custom-roles.md)。
>
>

> [!NOTE]
> 還原磁碟之後，現在即可取得部署範本，您可以直接用來建立新的虛擬機器。 沒有其他不同的 PS Cmdlet 可建立加密/解密的受控/非受控虛擬機器。

結果工作詳細資料提供可查詢和部署的範本 URI。

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

僅部署範本來建立新的虛擬機器，如[此處](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)所述。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

下節列出使使用「VMConfig」檔案建立虛擬機器所需的步驟。

> [!NOTE]
> 強烈建議使用以上詳述的部署範本來建立虛擬機器。 本節 (1-6 點) 很快就會淘汰。

1. 查詢工作詳細資料的已還原磁碟內容。

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. 設定 Azure 儲存體內容，並還原為 JSON 組態檔。

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. 使用 JSON 組態檔來建立 VM 組態。

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. 連接作業系統磁碟與資料磁碟。 此步驟提供各種受控和已加密 VM 設定的範例。 使用適合您的 VM 設定的範例。

   * **非受控與未加密的 VM** - 如果是非受控、未加密的 VM，請使用下列範例。

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **含 Azure AD 的非受控已加密 VM (僅限 BEK)** - 對於含 Azure AD 的非受控已加密 VM (僅限使用 BEK 加密)，您需要先將密碼還原至金鑰保存庫，才可以連結磁碟。 如需詳細資訊，請參閱[從 Azure 備份復原點還原已加密的虛擬機器](backup-azure-restore-key-secret.md)。 下列範例示範如何將 OS 和資料磁碟連結至已加密的 VM。 設定 OS 磁碟時，請務必提及相關的 OS 類型。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **含 Azure AD 的非受控已加密 VM (BEK 和 KEK)** - 對於含 Azure AD 的非受控已加密 VM (使用 BEK 和 KEK 加密)，您需要先將金鑰與密碼還原到金鑰保存庫，才可以連結磁碟。 如需詳細資訊，請參閱[從 Azure 備份復原點還原已加密的虛擬機器](backup-azure-restore-key-secret.md)。 下列範例示範如何將 OS 和資料磁碟連結至已加密的 VM。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **不含 Azure AD 的非受控已加密 VM (僅限 BEK)** - 對於不含 Azure AD 的非受控已加密 VM (僅限使用 BEK 加密)，如果**無法使用來源 keyVault/密碼**，會使用[從 Azure 備份復原點還原未加密的虛擬機器](backup-azure-restore-key-secret.md)中的程序，將密碼還原至金鑰保存庫。 然後執行下列指令碼，對於還原的 OS blob 設定加密詳細資料 (資料 blob 不需要此步驟)。 可以從還原的 keyVault 擷取 $dekurl。<br>

   只有在沒有來源 keyVault/密碼可用時，才需要執行下列指令碼。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    有**密碼可用**，而且也對於 OS Blob 設定加密詳細資料後，可使用下列指令碼附加磁碟。<br>

    如果來源 keyVault/密碼可供使用，則無法執行上述指令碼。

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **不含 Azure AD 的非受控已加密 VM (BEK 和 KEK)** - 對於不含 Azure AD 的非受控已加密 VM (僅限使用 BEK 和 KEK 加密)，如果**無法使用來源 keyVault/金鑰/密碼**，會使用[從 Azure 備份復原點還原未加密的虛擬機器](backup-azure-restore-key-secret.md)中的程序，將金鑰和密碼還原至金鑰保存庫。 然後執行下列指令碼，對於還原的 OS blob 設定加密詳細資料 (資料 blob 不需要此步驟)。 可以從還原的 keyVault 擷取 $dekurl 和 $kekurl。

   只有在沒有來源 keyVault/金鑰/密碼可用時，才需要執行下列指令碼。

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   有**金鑰/密碼可用**，而且對於 OS Blob 設定加密詳細資料後，可使用下列指令碼附加磁碟。

    如果來源 keyVault/金鑰/密碼可供使用，則無法執行上述指令碼。

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **受控與未加密的 VM** - 針對受控、未加密的 VM，請連結已還原的受控磁碟。 如需深入的資訊，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

   * **含 Azure AD 的受控已加密 VM (僅限 BEK)** - 對於含 Azure AD 的受控已加密的 VM (僅限使用 BEK 加密)，請連結已還原的受控磁碟。 如需深入的資訊，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

   * **含 Azure AD 的受控已加密 VM (BEK 與 KEK)** - 對於含 Azure AD 的受控已加密的 VM (使用 BEK 與 KEK 加密)，請連結已還原的受控磁碟。 如需深入的資訊，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

   * **不含 Azure AD 的受控已加密 VM (僅限 BEK)** - 對於不含 Azure AD 的受控已加密 VM (僅限使用 BEK 加密)，如果**無法使用來源 keyVault/密碼**，會使用[從 Azure 備份復原點還原未加密的虛擬機器](backup-azure-restore-key-secret.md)中的程序，將密碼還原至金鑰保存庫。然後執行下列指令碼，對於還原的 OS 磁碟設定加密詳細資料 (資料磁碟不需要此步驟)。 可以從還原的 keyVault 擷取 $dekurl。

     只有在沒有來源 keyVault/密碼可用時，才需要執行下列指令碼。  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     有密碼可用，而且也對於 OS 磁碟設定加密詳細資料後，若要附加已還原的受控磁碟，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

   * **不含 Azure AD 的受控已加密 VM (BEK 和 KEK)** - 對於不含 Azure AD 的受控已加密 VM (使用 BEK 和 KEK 加密)，如果**無法使用來源 keyVault/金鑰/密碼**，會使用[從 Azure 備份復原點還原未加密的虛擬機器](backup-azure-restore-key-secret.md)中的程序，將金鑰和密碼還原至金鑰保存庫。 然後執行下列指令碼，對於還原的 OS 磁碟設定加密詳細資料 (資料磁碟不需要此步驟)。 可以從還原的 keyVault 擷取 $dekurl 和 $kekurl。

   只有在沒有來源 keyVault/金鑰/密碼可用時，才需要執行下列指令碼。

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    有金鑰/密碼可用，而且對於 OS 磁碟設定加密詳細資料後，若要附加已還原的受控磁碟，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

5. 設定網路設定。

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. 建立虛擬機器。

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. 推送 ADE 擴充功能。

   * **對於含 Azure AD 的 VM** - 使用下列命令，以手動方式啟用資料磁碟的加密  

     **僅限 BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK 和 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **對於不含 Azure AD 的 VM** - 使用下列命令，以手動方式啟用資料磁碟的加密。

     如果在執行期間需要 AADClientID，則您需要更新您的 Azure PowerShell。

     **僅限 BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK 和 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>從 Azure VM 備份還原檔案

除了還原磁碟，您也可以從 Azure VM 備份還原個別檔案。 還原檔案功能提供復原點中所有檔案的存取權。 透過檔案總管管理檔案，就像一般的檔案一樣。

從 Azure VM 備份還原檔案的基本步驟如下：

* 選取 VM
* 選擇復原點
* 掛接復原點的磁碟
* 複製所需的檔案
* 將磁碟取消掛接

### <a name="select-the-vm"></a>選取 VM

若要取得可識別正確備份項目的 PowerShell 物件，請從保存庫中的容器開始，向下深入物件階層。 若要选择代表 VM 的容器，请使用 [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet，然后通过管道将其传递给 [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>選擇復原點

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) Cmdlet 來列出備份項目的所有復原點。 接下來選擇要還原的復原點。 如果您不確定要使用哪一個復原點，在清單中選擇最近的 RecoveryPointType = AppConsistent 點是好的做法。

在下列指令碼中，變數 **$rp** 是選取的備份項目在過去七天的復原點陣列。 陣列是以相反時間順序排序，最新復原點位於索引 0。 使用標準 PowerShell 陣列索引來挑選復原點。 在範例中，$rp[0] 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

輸出類似於下列範例：

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>掛接復原點的磁碟

请使用 [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) cmdlet 获取用于装载恢复点的所有磁盘的脚本。

> [!NOTE]
> 磁碟會以 iSCSI 連接磁碟的形式掛接至執行指令碼所在的機器。 掛接會立即發生，您不會產生任何費用。
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

輸出類似於下列範例：

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

在您要復原檔案的機器上執行此指令碼。 若要執行指令碼，您必須輸入提供的密碼。 連結磁碟之後，使用 Windows 檔案總管瀏覽新的磁碟區與檔案。 如需詳細資訊，請參閱「備份」文章：[從 Azure 虛擬機器備份復原檔案](backup-azure-restore-files-from-vm.md)。

### <a name="unmount-the-disks"></a>將磁碟取消掛接

复制所需的文件后，请使用 [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) 卸载磁盘。 請務必取消掛接磁碟，以便移除對復原點檔案的存取權。

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>後續步驟

如果你更愿意使用 PowerShell 来处理 Azure 资源，请查看 PowerShell 文章：[为 Windows Server 部署和管理备份](backup-client-automation.md)。 如果您管理 DPM 備份，請參閱[部署及管理 DPM 的備份](backup-dpm-automation.md)一文。
