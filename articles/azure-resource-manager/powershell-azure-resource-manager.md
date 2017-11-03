---
title: "Hantera Azure lösningar med PowerShell | Microsoft Docs"
description: "Använda Azure PowerShell och Resource Manager för att hantera dina resurser."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Hantera resurser med Azure PowerShell och Resource Manager

I den här artikeln lär du dig att hantera dina lösningar med Azure PowerShell och Azure Resource Manager. Om du inte är bekant med Resource Manager finns [översikt över Resource Manager](resource-group-overview.md). Den här artikeln fokuserar på hanteringsuppgifter. Du kommer att:

1. Skapa en resursgrupp
2. Lägg till en resurs i resursgruppen.
3. Lägga till en etikett till resursen
4. Fråga resurser baserat på namn eller värden
5. Använda och ta bort ett lås på resursen
6. Ta bort en resursgrupp

Den här artikeln visar inte hur du distribuerar en Resource Manager-mall till din prenumeration. Den här informationen finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>Kom igång med Azure PowerShell

Om du inte har installerat Azure PowerShell, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

Om du har installerat Azure PowerShell tidigare men inte har uppdaterat den nyligen kan du överväga att installera den senaste versionen. Du kan uppdatera versionen via samma metod som du använde för att installera den. Om du använder Web Platform Installer, starta den igen och leta efter en uppdatering.

Om du vill kontrollera din version av Azure-resurser modulen, använder du följande cmdlet:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Den här artikeln har uppdaterats för version 3.3.0. Om du har en tidigare version, överensstämmer din upplevelse inte med de steg som visas i den här artikeln. Dokumentation om cmdletar i den här versionen finns [AzureRM.Resources modulen](/powershell/module/azurerm.resources).

## <a name="log-in-to-your-azure-account"></a>Logga in på ditt Azure-konto
Innan du arbetar med din lösning, måste du logga in på ditt konto.

Om du vill logga in på ditt Azure-konto, använder den **Login-AzureRmAccount** cmdlet.

```powershell
Login-AzureRmAccount
```

Den här cmdleten uppmanar dig att ange inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell.

Cmdlet returnerar information om ditt konto och prenumeration ska användas för uppgifter.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Om du har mer än en prenumeration kan växla du till en annan prenumeration. Först ska vi se alla prenumerationer för ditt konto.

```powershell
Get-AzureRmSubscription
```

Den returnerar aktiverade och inaktiverade prenumerationer.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Ange om du vill växla till en annan prenumeration prenumerationsnamn med den **Set-AzureRmContext** cmdlet.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du distribuerar några resurser till din prenumeration, måste du skapa en resursgrupp som innehåller resurser.

Du kan skapa en resursgrupp med det **New-AzureRmResourceGroup** cmdlet. Kommandot använder den **namn** parametern för att ange ett namn för resursgruppen och **plats** parametern för att ange dess plats.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

Utdata är i följande format:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Om du behöver hämta resursgruppen senare använder du följande cmdlet:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

För att få alla resursgrupper i din prenumeration kan du inte ange ett namn:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Lägg till resurser i en resursgrupp

Om du vill lägga till en resurs i resursgruppen som du kan använda den **ny AzureRmResource** cmdlet eller en cmdlet som är specifik för typ av resurs som skapas (t.ex. **New-AzureRmStorageAccount**). Det kan vara enklare att använda en cmdlet som är specifika för en resurstyp eftersom den innehåller parametrar för de egenskaper som behövs för den nya resursen. Att använda **ny AzureRmResource**, måste du känna till alla egenskaperna för att ange utan som efterfrågas.

Lägga till en resurs via cmdlets kan orsaka framtida förvirring eftersom den nya resursen inte finns i en Resource Manager-mall. Microsoft rekommenderar att du definierar infrastrukturen för din Azure-lösning i en Resource Manager-mall. Mallar kan du distribuerar din lösning på ett tillförlitligt sätt och flera gånger. Du skapar ett lagringskonto med en PowerShell-cmdlet för den här artikeln, men senare du generera en mall från resursgruppen.

Följande cmdlet skapar ett lagringskonto. Ange ett unikt namn för storage-konto i stället för med hjälp av namnet som visas i exemplet. Namnet måste vara mellan 3 och 24 tecken långt och innehålla endast siffror och gemener. Om du använder det namn som visas i exemplet felmeddelande ett eftersom namnet redan används.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Om du behöver hämta den här resursen senare använder du följande cmdlet:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Lägga till en tagg

Taggar kan du organisera dina resurser efter olika egenskaper. Du kan till exempel ha flera resurser i olika resursgrupper som tillhör samma avdelning. Du kan använda en avdelning taggen och ett värde till dessa resurser för att markera dem som tillhör samma kategori. Eller så kan du markera om en resurs används i en produktionsmiljö eller testmiljö. Du lägger till taggar till en resurs i den här artikeln, men i din miljö det mest sannolika klokt att lägga till taggar för dina resurser.

Följande cmdlet gäller två taggar för ditt lagringskonto:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

Taggar uppdateras som ett enskilt objekt. Om du vill lägga till en tagg i en resurs som redan omfattar taggar, att hämta befintliga taggar. Lägg till ny tagg i det objekt som innehåller befintliga taggar och tillämpa alla taggar till resursen.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Sök efter resurser

Använd den **hitta AzureRmResource** för att hämta resurser för olika sökvillkor.

* För att få en resurs med namnet, ange den **ResourceNameContains** parameter:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Om du vill hämta alla resurser i en resursgrupp, ange den **ResourceGroupNameContains** parameter:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Om du vill hämta alla resurser med taggnamn och värde, ange den **TagName** och **TagValue** parametrar:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Alla resurser med en viss resurstyp, ange den **ResourceType** parameter:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>Hämta resurs-ID

Många kommandon ta en resurs-ID som en parameter. Om du vill hämta ID för en resurs och lagra i en variabel, använder du:

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>Låsa en resurs

När du behöver se till att en kritisk resurs tas inte bort av misstag eller ändras kan tillämpa ett lås på resursen. Du kan ange antingen en **CanNotDelete** eller **ReadOnly**.

För att skapa eller ta bort management lås, måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. I de inbyggda rollerna beviljas endast ägare och administratör för användaråtkomst dessa åtgärder.

Om du vill använda ett lås, använder du följande cmdlet:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Låst resursen i föregående exempel kan inte tas bort förrän låset tas bort. Ta bort ett lås med:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Mer information om inställningen Lås finns [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Ta bort resurser eller resursgrupp
Du kan ta bort en resurs eller en resursgrupp. När du tar bort en resursgrupp kan du också ta bort alla resurser i resursgruppen.

* Ta bort en resurs från resursgruppen genom att använda den **ta bort AzureRmResource** cmdlet. Den här cmdleten tar bort resursen, men tar inte bort resursgruppen.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Ta bort en resursgrupp och alla dess resurser genom att använda den **Remove-AzureRmResourceGroup** cmdlet.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

För båda cmdlets uppmanas du att bekräfta att du vill ta bort resurs eller resursgrupp. Om åtgärden har tar bort en resurs eller en resursgrupp, returnerar **SANT**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Kör skript för Resource Manager med Azure Automation

Den här artikeln visar hur du utför grundläggande åtgärder på resurser med Azure PowerShell. För mer avancerade scenarier vill du förmodligen att skapa ett skript och återanvända skriptet efter behov eller enligt ett schema. [Azure Automation](../automation/automation-intro.md) ger ett sätt att automatisera vanliga skript som hanterar dina Azure-lösningar som används.

De följande avsnitten visar hur du använder Azure Automation, Resource Manager och PowerShell för att effektivt utföra administrativa uppgifter:

- Information om hur du skapar en runbook finns [min första PowerShell-runbook](../automation/automation-first-runbook-textual-powershell.md).
- Information om hur du arbetar med gallerier av skript finns [Azure Automation Runbook- och stänga](../automation/automation-runbook-gallery.md).
- Runbooks som kan starta och stoppa virtuella datorer, se [Azure Automation-scenario: använda JSON-formaterad taggar om du vill skapa ett schema för Virtuella Azure-start och stopp](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Runbooks som kan starta och stoppa virtuella datorer låg belastning på nätverket, se [Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning i Automation](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skapar Resource Manager-mallar i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Läs om hur du distribuerar mallar i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).
* Du kan flytta befintliga resurser till en ny resursgrupp. Exempel finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

