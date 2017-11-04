---
title: "Skapa en tilldelning av principer för att identifiera icke-kompatibla resurser i Azure-miljön med hjälp av PowerShell | Microsoft Docs"
description: "Använd PowerShell för att skapa en Azure principtilldelning för att identifiera resurser som icke-kompatibla."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 02afe946e5e1ad9730ab07df19676e90485ecf98
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Skapa en tilldelning av principer för att identifiera icke-kompatibla resurser i Azure-miljön med hjälp av PowerShell

Det första steget i att förstå efterlevnad i Azure är viktigt att veta där du stå med aktuella resurserna. Denna Snabbstart vägleder dig genom processen att skapa en tilldelning av principer för att identifiera virtuella datorer som inte använder hanterade diskar.

I slutet av den här processen har identifieras vilka virtuella datorer inte använder hanterade diskar och är därför *icke-kompatibla*.


PowerShell används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden information som använder PowerShell för att skapa en tilldelning av principer för att identifiera icke-kompatibla resurser i Azure-miljön.

Den här guiden kräver Azure PowerShell Modulversion 4.0 eller senare. Kör ```Get-Module -ListAvailable AzureRM``` versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Innan du börjar bör du kontrollera att den senaste versionen av PowerShell har installerats. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="opt-in-to-azure-policy"></a>Välja att Azure-princip

Azure princip är nu tillgänglig som förhandsversion och du behöver registrera att begära åtkomst.

1. Gå till Azure-princip på https://aka.ms/getpolicy och välj **registrera dig** i den vänstra rutan.

   ![Sök efter princip](media/assign-policy-definition/sign-up.png)

2. Välja att Azure-principen genom att välja prenumerationer i den **prenumeration** listan som du vill arbeta med. Välj sedan **registrera**.

   ![Välj för att använda Azure-princip](media/assign-policy-definition/preview-opt-in.png)

   Din förfrågan godkänns automatiskt för förhandsgranskning. Vänta i upp till 30 minuter för systemet för att bearbeta din registrering.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten vi skapa en principtilldelning och tilldela den *Audit virtuella datorer utan diskar hanteras* definition. Definitionen för den här principen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Följ dessa steg om du vill skapa en ny tilldelning av principer.

Kör följande kommando för att visa alla principdefinitioner och hittar det du vill tilldela:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Azure principen har redan inbyggda principdefinitioner som du kan använda. Visas inbyggda principdefinitioner som:

- Framtvinga taggen och dess värde
- Tillämpa taggen och dess värde
- Kräv SQL Server-Version 12.0

Tilldela sedan principdefinitionen till det önskade omfånget med hjälp av den `New-AzureRmPolicyAssignment` cmdlet.

Den här självstudien ger följande information för kommandot:
- Visa **namn** för tilldelning av principer. I det här fallet ska vi använda Audit virtuella datorer utan hanterade diskar.
- **Principen** – detta är principdefinitionen, baserade av som du använder för att skapa tilldelningen. I det här fallet är det principdefinitionen – *Audit virtuella datorer utan hanterade diskar*
- En **omfång** – ett omfång avgör vilka resurser eller gruppering av resurser hämtar principtilldelningen tillämpas på. Det kan röra sig om en prenumeration till resursgrupper. I det här exemplet vi tilldela principdefinitionen till den **FabrikamOMS** resursgruppen.
- **$definition** – du måste ange resurs-ID för principdefinitionen – i det här fallet använder vi ID för principdefinitionen - *Audit virtuella datorer utan diskar hanteras*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Du är nu redo att identifiera icke-kompatibla resurser för att förstå kompatibilitetsstatusen för din miljö.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

1. Gå tillbaka till Azure princip denna sida.
2. Välj **kompatibilitet** på den vänstra rutan och söka efter den **principtilldelning** du skapade.

   ![För principefterlevnad](media/assign-policy-definition/policy-compliance.png)

   Om det finns några befintliga resurser som inte är kompatibla med den nya tilldelningen, de kommer att visas under den **icke-kompatibel resurser** fliken som ovan.

## <a name="clean-up-resources"></a>Rensa resurser

Andra guider i den här samlingen bygger på denna Snabbstart. Om du vill fortsätta att arbeta med efterföljande självstudiekurser inte rensa upp de resurser som skapats i denna Snabbstart. Om du inte planerar att fortsätta, ta bort tilldelningen som du skapade genom att köra det här kommandot:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguide tilldelas en principdefinition att identifiera icke-kompatibla resurser i Azure-miljön.

Mer information om tilldelning av principer för att säkerställa att **framtida** resurser som skapas är kompatibla, även i fortsättningen vägledning för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
