---
title: Ange resursgrupp för virtuella datorer i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du anger en resursgrupp för virtuella datorer i ett labb i Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134534"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Ange en resursgrupp för virtuella labbdatorer i Azure DevTest Labs

Som labbägare kan du konfigurera de virtuella labbdatorerna så att de skapas i en viss resursgrupp. Den här funktionen hjälper dig i följande scenarier:

- Har färre resursgrupper som skapats av labb i din prenumeration.
- Låt dina labb fungera inom en fast uppsättning resursgrupper som du konfigurerar.
- Gå runt begränsningar och godkännanden som krävs för att skapa resursgrupper i din Azure-prenumeration.
- Konsolidera alla labbresurser inom en enda resursgrupp för att förenkla spårningen av dessa resurser och tillämpa [principer](../governance/policy/overview.md) för att hantera resurser på resursgruppsnivå.

Med den här funktionen kan du använda ett skript för att ange en ny eller befintlig resursgrupp i din Azure-prenumeration för alla dina virtuella labb-datorer. Azure DevTest Labs stöder för närvarande den här funktionen via ett API.

> [!NOTE]
> Alla prenumerationsgränser gäller när du skapar labb i DevTest Labs. Tänk på ett labb som vilken annan resurs som helst i din prenumeration. När det gäller resursgrupper är gränsen [980 resursgrupper per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Använda Azure-portalen
Följ dessa steg för att ange en resursgrupp för alla virtuella datorer som skapats i labbet. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på menyn till vänster. 
3. Välj **DevTest Labs** från listan.
4. Välj ditt **labb**i listan över labb.  
5. Välj **Konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn. 
6. Välj **Labbinställningar** på den vänstra menyn. 
7. Välj **Alla virtuella datorer i en resursgrupp**. 
8. Markera en befintlig resursgrupp i listrutan (eller) välj **Skapa ny**, ange ett **namn** för resursgruppen och välj **OK**. 

    ![Välj resursgrupp för alla virtuella labb-datorer](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Använd PowerShell 
I följande exempel visas hur du använder ett PowerShell-skript för att skapa alla virtuella labbdatorer i en ny resursgrupp.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Anropa skriptet med hjälp av följande kommando. ResourceGroup.ps1 är filen som innehåller föregående skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager-mall
Om du använder en Azure Resource Manager-mall för att skapa ett labb använder du egenskapen **vmCreationResourceGroupId** i avsnittet labbegenskaper i mallen, vilket visas i följande exempel:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API för att konfigurera en resursgrupp för labb-virtuella datorer
Du har följande alternativ som labbägare när du använder det här API:et:

- Välj **labbets resursgrupp** för alla virtuella datorer.
- Välj en annan **befintlig resursgrupp** än labbets resursgrupp för alla virtuella datorer.
- Ange ett **nytt resursgruppsnamn** för alla virtuella datorer.
- Fortsätt att använda det befintliga beteendet, där en resursgrupp skapas för varje virtuell dator i labbet.
 
Den här inställningen gäller för nya virtuella datorer som skapats i labbet. De äldre virtuella datorerna i labbet som har skapats i sina egna resursgrupper påverkas inte. Miljöer som skapas i labbet fortsätter att finnas kvar i sina egna resursgrupper.

Så här använder du det här API:et:
- Använd API-version **2018_10_15_preview**.
- Om du anger en ny resursgrupp kontrollerar du att du har **skrivbehörighet för resursgrupper** i prenumerationen. Om du saknar skrivbehörighet misslyckas det att skapa nya virtuella datorer i den angivna resursgruppen.
- När du använder API:et skickar du in **hela resursgrupp-ID.** Till exempel: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Kontrollera att resursgruppen har samma prenumeration som labbet. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-get-started-with-lab-policies.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
