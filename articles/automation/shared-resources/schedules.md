---
title: Azure 自動化中的排程
description: 自動化排程是用來排程讓 Azure 自動化中的 Runbook 自動啟動。 描述如何建立和管理排程，以便以特定時間或循環排程來自動啟動 Runbook。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 483f9092d29fc40937ed9d54510269af2af30872
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128799"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>在 Azure 自動化中排程 Runbook

若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。 您可以在 Azure 入口網站中，將 Runbook 排程設定成執行一次，或是每小時或每天重複發生。 您也可以將其排定為在每週、每月、特定的星期幾、月份中的某些日子或月份中的特定日子執行。 Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

> [!NOTE]
> 排程目前不支援 Azure Automation DSC 組態。

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

下表中的 cmdlet 用于在 Azure 自动化中通过 PowerShell 创建和管理计划。 它們是隨著 [Azure PowerShell 模組](/powershell/azure/overview)的一部分推出。

| Cmdlet | 描述 |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |擷取排程。 |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |建立新排程。 |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |移除排程。 |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |設定現有排程的屬性。 |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |擷取排程的 Runbook。 |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |將 Runbook 與排程相關聯。 |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |從排程分離 Runbook。 |

## <a name="creating-a-schedule"></a>建立排程

可以使用 Azure 门户或 PowerShell 为 Runbook 创建新计划。

> [!NOTE]
> 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。  為了避免影響 Runbook 和它們所自動化的處理序，您應該先使用專用於測試的自動化帳戶測試任何具有連結排程的 Runbook。  這會驗證您排程的 Runbook 會持續正確運作，否則，您可以進一步進行疑難排解，並套用任何必要變更，再將已更新的 Runbook 版本移轉至生產環境。
> 除非您已選取 [模組] 中的[更新 Azure 模組](../automation-update-azure-modules.md)選項進行手動更新，否則自動化帳戶不會自動取得任何新版本的模組。

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>在 Azure 入口網站中建立新排程

1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [共用資源] 區段底下的 [排程]。
2. 在分頁的頂端按一下 [加入排程]。
3. 在 [新增排程] 窗格中，為新排程輸入 [名稱] 並選擇性地輸入 [描述]。
4. 透過選取 [一次] 或 [定期]，以決定排程僅執行一次或定期執行。 如果選取 [一次]，請指定 [開始時間]，然後按一下 [建立]。 如果您選取 [定期]，請指定 [開始時間]，並針對 [重複頻率] 選取所需的 Runbook 重複頻率：按 [小時]、[天]、[週] 或 [月] 執行。
    1. 如果选择“周”，则会显示一周中可供选择的日期列表。 選取您需要的天數。 首次執行排程的時間將會是開始時間後所選的第一天。 例如，若要选择周末计划，请选择“星期六”和“星期日”。

       ![设置周末重复计划](../media/schedules/week-end-weekly-recurrence.png)

    2. 如果选择“月”，则会看到不同的选项。 对于“每月进行次数”选项，请选择“每月天数”或“每周天数”。 如果选择“月份日期”，则会显示一个可根据需要选择天数的日历。 如果选择当月不存在的日期（例如 31 日），则计划将不会运行。 如果您希望在最後一天執行排程，請在 [在每月最後一天執行] 下選取 [是]。 如果您選擇 [星期]，系統會顯示 [重複頻率] 選項。 選擇 [第一週]、[第二週]、[第三週]、[第四週] 或 [最後一週]。 最後，請選擇重複執行的日期。

       ![在每月 1 号、15 号和最后一天运行的计划](../media/schedules/monthly-first-fifteenth-last.png)

5. 完成後，按一下 [建立]。

### <a name="to-create-a-new-schedule-with-powershell"></a>使用 PowerShell 创建新计划

使用 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet 來建立排程。 您必須指定排程的開始時間，以及其應該執行的頻率。 下列範例示範如何建立許多不同的排程案例。

#### <a name="create-a-one-time-schedule"></a>创建一次性计划

以下示例命令将创建一次性的计划。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>创建重复计划

以下示例命令演示如何创建在一年中的每天下午 1:00 运行的重复计划。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>创建每周重复计划

以下示例命令演示如何创建仅在工作日运行的每周计划。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>创建在工作日运行的每周重复计划

以下示例命令演示如何创建仅在周末运行的每周计划。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>创建在当月 1 号、15 号和最后一天运行的重复计划

以下示例命令演示如何创建在某月的 1 号、15 号和最后一天运行的重复计划。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>將排程連結至 Runbook

Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。 如果 Runbook 有參數，您可以為它們提供值。 您必須為任何必要參數提供值，並可以提供任何選擇性參數的值。 每次此排程啟動 Runbook 時會使用這些值。 您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站將排程連結至 Runbook

1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [程序自動化] 區段底下的 [Runbook]。
2. 按一下要排程的 Runbook 名稱。
3. 如果 Runbook 当前未链接到计划，则系统会提供“创建新计划”或“链接到现有计划”选项。
4. 如果 Runbook 有参数，可以选择选项“修改运行设置(默认值:Azure)”，此时会显示“参数”窗格，可在其中输入信息。

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>使用 PowerShell 将计划链接到 Runbook

您可以使用 [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) Cmdlet 來連結排程。 您可以使用 Parameters 參數來指定 Runbook 參數的值。 如需如何指定參數值的詳細資訊，請參閱[在 Azure 自動化中啟動 Runbook](../automation-starting-a-runbook.md)。
下列範例命令顯示如何使用 Azure Resource Manager Cmdlet 與參數，將排程連結至 Runbook。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>更頻繁地排程 Runbook

可為 Azure 自動化中的排程設定的最頻繁間隔是一小時。 如果您需要讓排程以比一小時更頻繁地頻率來執行，您有兩個選項：

* 為 Runbook 建立 [Webhook](../automation-webhooks.md) 並使用 [Azure 排程器](../../scheduler/scheduler-get-started-portal.md)來呼叫此 Webhook。 Azure 排程器在定義排程時，可提供更細微的方式。

* 建立四個排程，讓其每小時執行一次，且彼此全都會在相差 15 分鐘內開始。 此案例可讓 Runbook 使用不同排程每 15 分鐘執行一次。

## <a name="disabling-a-schedule"></a>停用排程

停用排程時，與其連結的任何 Runbook 無法再依該排程執行。 您可以手動停用排程，或在建立排程時為具有頻率的排程設定到期時間。 达到过期时间时，会禁用该计划。

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>從 Azure 入口網站停用排程

1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [共用資源] 區段底下的 [排程]。
2. 按一下排程的名稱以開啟詳細資料窗格。
3. 將 [已啟用] 變更為 [否]。

> [!NOTE]
> 若要禁用开始时间已过去的计划，必须将开始日期更改为将来的某个时间，然后保存计划。

### <a name="to-disable-a-schedule-with-powershell"></a>使用 PowerShell 禁用计划

您可以使用 [Set-AzureAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) Cmdlet 變更現有排程的屬性。 若要停用排程，請為 **IsEnabled** 參數指定 **false**。

下列範例命令顯示如何使用 Azure Resource Manager Cmdlet 來停用 Runbook 的排程。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>後續步驟

* 若要在 Azure 自动化中开始使用 Runbook，请参阅 [在 Azure 自动化中启动 Runbook](../automation-starting-a-runbook.md)

