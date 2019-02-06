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
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749008"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Ange en resursgrupp för labbet virtuella datorer i Azure DevTest Labs
Som labbägare kan konfigurera du dina virtuella datorer för testlabbet skapas i en specifik resursgrupp. Använd den här funktionen så att du inte når resursen gruppgränser på din Azure-prenumeration. Den här funktionen kan du konsolidera alla labbresurser inom en enda resursgrupp. Det förenklar också spåra dessa resurser och tillämpa [principer](../governance/policy/overview.md) kan hantera dem på resursgruppsnivå.

Med den här funktionen kan använda du ett skript för att ange en ny eller en befintlig resursgrupp i Azure-prenumerationen för ditt labb virtuella datorer. DevTest Labs stöder för närvarande den här funktionen via ett API. 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>API för att konfigurera en resursgrupp för labb virtuella datorer
Nu ska vi gå igenom vilka alternativ som finns som labbägare när du använder den här API: 

- Du kan välja den **övningen resursgrupp** för alla virtuella datorer.
- Du kan välja en **befintlig resursgrupp** än den testmiljön resursgrupp för alla virtuella datorer.
- Du kan ange en **ny resursgrupp** namn för alla virtuella datorer.
- Du kan fortsätta med den befintliga funktionen, det vill säga en resursgrupp skapas för varje virtuell dator i labbet.
 
Den här inställningen gäller för nya virtuella datorer som skapas i labbet. De äldre virtuella datorer i labbet som har skapats i sina egna resursgrupper fortsätta att påverkas. Men kan du migrera de virtuella datorerna från deras enskilda resursgrupper till vanliga resursgruppen så att alla dina virtuella datorer för testlabbet finns i en gemensam resursgrupp. Mer information finns i [flytta resurser till en ny resursgrupp](../azure-resource-manager/resource-group-move-resources.md). Miljöer som skapats i labbet ska fortsätta vara i sina egna resursgrupper.

### <a name="how-to-use-this-api"></a>Hur du använder den här API:
- Använd API-versionen **2018_10_15_preview** när du använder detta API. 
- Om du anger en ny resursgrupp måste du kontrollera att du har **skrivbehörighet på resursgrupper** i din prenumeration. Skapa nya virtuella datorer utan skrivbehörighet, i angivna resource group resulterar i ett fel. 
- När du använder API: et kan skicka in den **fullständig resursgrupps-ID**. Till exempel: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Kontrollera att resursgruppen finns i samma prenumeration som labbet. 

## <a name="use-powershell"></a>Använd PowerShell 
Följande exempel beskrivs hur du skapar alla virtuella datorer för testlabbet i en ny resursgrupp med hjälp av ett PowerShell-skript.

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

Anropa skriptet med följande kommando (ResourceGroup.ps1 är den fil som innehåller det föregående skript): 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Använd Azure Resource Manager-mallar
Om du använder Azure Resource Manager-mall för att skapa ett labb, använder du den **vmCreationResourceGroupId** -egenskapen i labbavsnitt för egenskaper för Resource Manager-mallen som du ser i följande exempel:

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
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-get-started-with-lab-policies.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
