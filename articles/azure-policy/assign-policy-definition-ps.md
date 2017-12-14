---
title: "Använda PowerShell för att skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön | Microsoft Docs"
description: "Använda PowerShell för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6a9b7cff1341bd898b76a226ca413b8135eec408
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön med PowerShell

Det första steget för att förstå efterlevnad i Azure är att veta hur du ligger till med dina befintliga resurser. Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

I slutet av den här processen kommer du att ha lyckats identifiera virtuella datorer som inte använder hanterade diskar och därför är *icke-kompatibla*.


PowerShell används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här guiden får du lära dig hur man använder PowerShell för att skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön.

Den här guiden kräver Azure PowerShell-modul version 4.0 eller senare. Kör ```Get-Module -ListAvailable AzureRM``` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Innan du börjar bör du kontrollera att den senaste versionen av PowerShell har installerats. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.


## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar vi en principtilldelning och tilldelar principdefinitionen *Granska virtuella datorer utan Managed Disks*. Den här principdefinitionen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Följ de här stegen om du vill skapa en ny principtilldelning.

Kör följande kommando för att visa alla principdefinitioner och hitta den som du vill tilldela:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Azure Policy har inbyggda principdefinitioner som du kan använda. Du kan se inbyggda principdefinitioner som:

- Framtvinga tagg och dess värde
- Använd tagg och dess värde
- Kräv SQL Server Version 12.0

Tilldela principdefinitionen till önskad omfattning med hjälp av cmdleten `New-AzureRmPolicyAssignment`.

I den här självstudien tillhandahåller vi följande information för kommandot:
- **Name** – namn som visas för principtilldelningen. I det här fallet använder vi Granska virtuella datorer utan Managed Disks.
- **Policy** – detta är principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionen *Granska virtuella datorer utan Managed Disks*.
- **Scope** – en omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. I det här exemplet ska vi tilldela principdefinitionen till resursgruppen **FabrikamOMS**.
- **$definition** – du måste ange resurs-ID för principdefinitionen, i det här fallet använder vi principdefinitionens ID *Granska virtuella datorer utan Managed Disks*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetsstatus.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

1. Gå tillbaka till landningssidan för Azure Policy.
2. Välj **Efterlevnad** i det vänstra fönstret och sök efter den **principtilldelning** du skapade.

   ![Principefterlevnad](media/assign-policy-definition/policy-compliance.png)

   Om det finns några befintliga resurser som inte är kompatibla med denna nya tilldelning visas de på fliken **Icke-kompatibla resurser** (se ovan).

## <a name="clean-up-resources"></a>Rensa resurser

De andra guiderna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta tar du bort tilldelningen som du skapade genom att köra det här kommandot:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill lära dig mer om att tilldela principer för att se till att **framtida** resurser som skapas är kompatibla kan du gå vidare till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
