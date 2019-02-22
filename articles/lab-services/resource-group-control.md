---
title: Ange resursgrupp för virtuella datorer i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att ange en resursgrupp för virtuella datorer i ett labb i Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: e4e2a01bbac7aebb70852b93c51c32933cc75eec
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652186"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Ange en resursgrupp för labbet virtuella datorer i Azure DevTest Labs

Som labbägare kan konfigurera du dina virtuella datorer för testlabbet skapas i en specifik resursgrupp. Den här funktionen hjälper dig att i följande scenarier:

- Har färre resursgrupper som skapats av labs i din prenumeration.
- Ha dina labb fungera inom en fast uppsättning resursgrupper som du konfigurerar.
- Undvika begränsningar och godkännanden som krävs för att skapa resursgrupper i Azure-prenumerationen.
- Konsolidera alla labbresurser inom en enskild resursgrupp för att förenkla spåra dessa resurser och tillämpa [principer](../governance/policy/overview.md) att hantera resurser på resursgruppsnivå.

Med den här funktionen kan använda du ett skript för att ange en ny eller befintlig resursgrupp i Azure-prenumerationen för ditt labb virtuella datorer. Azure DevTest Labs stöder för närvarande den här funktionen via ett API.

## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API för att konfigurera en resursgrupp för labbet virtuella datorer
Du har följande alternativ som labbägare när du använder den här API:

- Välj den **övningen resursgrupp** för alla virtuella datorer.
- Välj en **befintlig resursgrupp** än den testmiljön resursgrupp för alla virtuella datorer.
- Ange en **ny resursgrupp** namn för alla virtuella datorer.
- Fortsätt att använda den befintliga funktionen, som en resursgrupp skapas för varje virtuell dator i labbet.
 
Den här inställningen gäller för nya virtuella datorer som skapas i labbet. De äldre virtuella datorer i labbet som har skapats i sina egna resursgrupper påverkas. Miljöer som skapas i labbet ska fortsätta vara i sina egna resursgrupper.

Hur du använder den här API:
- Använd API-version **2018_10_15_preview**.
- Om du anger en ny resursgrupp måste du kontrollera att du har **skrivbehörighet på resursgrupper** i din prenumeration. Om du saknar behörighet att skriva, misslyckas skapa nya virtuella datorer i den angivna resursgruppen.
- När du använder API: et kan skicka in den **fullständig resursgrupps-ID**. Till exempel: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Kontrollera att resursgruppen finns i samma prenumeration som labbet. 

## <a name="use-powershell"></a>Använd PowerShell 
I följande exempel visas hur du använder ett PowerShell-skript för att skapa alla virtuella datorer för testlabbet i en ny resursgrupp.

```PowerShell
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

Anropa skriptet med hjälp av följande kommando. ResourceGroup.ps1 är den fil som innehåller det här skriptet:

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Använd en Azure Resource Manager-mall
Om du använder en Azure Resource Manager-mall för att skapa ett labb, använder du den **vmCreationResourceGroupId** -egenskapen i avsnittet lab egenskaper i din mall som du ser i följande exempel:

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


## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-get-started-with-lab-policies.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
