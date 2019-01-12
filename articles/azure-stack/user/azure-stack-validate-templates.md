---
title: Använd ett verktyg för verifiering av mallen för att kontrollera mallar för Azure Stack | Microsoft Docs
description: Kontrollera mallar för distribution till Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4ec617fe49a3b55ba0608b77a8d1226fae025e6c
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246457"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Kontrollera dina mallar för Azure Stack med verktyget för mall-validering

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda verktyget mall verifiering för att kontrollera om Azure Resource Manager [mallar](azure-stack-arm-templates.md) är redo för att distribuera till Azure Stack. Mallen verifiering verktyget är tillgängligt som en del av Azure Stack-verktyg. Hämta Azure Stack-verktyg med hjälp av stegen som beskrivs i den [ladda ned verktyg från GitHub](azure-stack-powershell-download.md) artikeln.

## <a name="overview"></a>Översikt

För att verifiera en mall kan du behöva och skapa ett moln funktioner filen först och sedan köra verktyget verifiering. Du kan använda följande PowerShell-moduler från Azure Stack-verktyg:

- I den **CloudCapabilities** mapp:<br>         `AzureRM.CloudCapabilities.psm1` skapar en cloud funktioner JSON-fil som representerar de tjänster och -versioner i ett Azure Stack-moln.
- I den **TemplateValidator** mapp:<br>
`AzureRM.TemplateValidator.psm1` använder en cloud funktioner JSON-fil för att testa mallar för distribution i Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Skapa filen cloud-funktioner

Innan du använder mallen verifieraren kan köra den **AzureRM.CloudCapabilities** PowerShell-modulen för att skapa en JSON-fil.

>[!NOTE]
>Om du uppdaterar din integrerat system eller Lägg till nya tjänster eller virtuella tillägg, bör du köra den här modulen igen.

1. Kontrollera att du är ansluten till Azure Stack. De här stegen kan utföras från Azure Stack development kit värden eller använda en [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) att ansluta från din arbetsstation.
2. Importera den **AzureRM.CloudCapabilities** PowerShell-modulen:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Använd den `Get-CloudCapabilities` cmdlet för att hämta service-versioner och skapa en cloud funktioner JSON-fil. Om du inte anger **- OutputPath**, filen AzureCloudCapabilities.Json har skapats i den aktuella katalogen. Använd din faktiska plats:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Kontrollera mallar

Använd de här stegen för att validera mallar med hjälp av den **AzureRM.TemplateValidator** PowerShell-modulen. Du kan använda egna mallar eller verifiera den [Azure Stack-snabbstartmallar](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importera den **AzureRM.TemplateValidator.psm1** PowerShell-modulen:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Köra verifieraren mall:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Mallen verifiering varningar eller fel loggas i PowerShell-konsolen och en HTML-fil i källkatalogen. Följande skärmdump visar ett exempel på en verifieringsrapport:

![Verifieringsrapport för mallen](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametrar

Mall-verifieraren stöder följande parametrar.

| Parameter | Beskrivning | Krävs |
| ----- | -----| ----- |
| TemplatePath | Anger sökvägen till rekursivt hitta Azure Resource Manager-mallar | Ja | 
| TemplatePattern | Anger namnet på mallfiler så att de matchar. | Nej |
| CapabilitiesPath | Anger sökvägen till JSON molnfiltjänster-funktioner | Ja | 
| IncludeComputeCapabilities | Innehåller utvärdering av IaaS-resurser som VM-storlekar och VM-tillägg | Nej |
| IncludeStorageCapabilities | Innehåller utvärderingen av lagringsresurser som SKU-typer | Nej |
| Rapport | Anger namnet på den genererade rapporten i HTML | Nej |
| Utförlig | Loggar fel och varningar till konsolen | Nej|

### <a name="examples"></a>Exempel

Det här exemplet verifierar att alla de [Azure Stack-snabbstartmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) laddas ned till lokal lagring. I exempel validerar också storlekar för virtuella datorer och tillägg mot Azure Stack Development Kit funktioner:

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Nästa steg

- [Distribuera mallar i Azure Stack](azure-stack-arm-templates.md)
- [Utveckla mallar för Azure Stack](azure-stack-develop-templates.md)
