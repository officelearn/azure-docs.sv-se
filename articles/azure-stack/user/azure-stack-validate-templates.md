---
title: "Använd mallen verifieraren för att kontrollera mallar för Azure-Stack | Microsoft Docs"
description: "Kontrollera mallar för distribution till Azure-stacken"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6a77efb3ef4236048ff08b14346175b592493982
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Kontrollera dina mallar för Azure-stacken med mallen verifieraren

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda verktyget mallen verifiering för att kontrollera om din Azure Resource Manager [mallar](azure-stack-arm-templates.md) är redo för Azure-stacken. Mallen valideringsverktyget är tillgängligt som en del av verktyg för Azure-stacken. Hämta Azure Stack-verktyg med hjälp av stegen som beskrivs i den [hämta verktyg från GitHub](azure-stack-powershell-download.md) artikel. 

Om du vill validera mallar kan du använda följande PowerShell-moduler i **TemplateValidator** och **CloudCapabilities** mappar: 

 - AzureRM.CloudCapabilities.psm1 skapar en molnet funktioner JSON-fil som representerar de tjänster och -versioner i ett moln som Azure-stacken.
 - AzureRM.TemplateValidator.psm1 använder en molnet funktioner JSON-fil för att testa mallar för distribution i Azure-stacken.
 
I den här artikeln, skapa en funktionsfil i molnet och kör sedan verktyget verifieraren.

## <a name="build-cloud-capabilities-file"></a>Skapa en funktionsfil i molnet
Innan du använder mallen verifieraren kan köra AzureRM.CloudCapabilities PowerShell-modulen för att skapa en JSON-fil. Om du uppdaterar din integrerat system eller Lägg till nya tjänster eller VM-tillägg måste du också köra att modulen igen.

1.  Kontrollera att du har en anslutning till Azure-stacken. Dessa åtgärder kan utföras från Azure-stacken development kit värden eller använda en [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) att ansluta från din arbetsstation. 
2.  Importera AzureRM.CloudCapabilities PowerShell-modulen:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Använd cmdleten Get-CloudCapabilities för att hämta service-versioner och skapa en moln funktioner JSON-fil. Om du inte anger - OutputPath, filen AzureCloudCapabilities.Json skapas i den aktuella katalogen. Använd din faktiska plats:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```             

## <a name="validate-templates"></a>Validera mallar
I följande steg ska validera du mallar med hjälp av AzureRM.TemplateValidator PowerShell-modulen. Du kan använda egna mallar eller verifiera den [Azure Stack-snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Importera AzureRM.TemplateValidator.psm1 PowerShell-modulen:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Kör verifieraren mallen:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Några mallen valideringsvarningar eller fel loggas PowerShell-konsolen och en HTML-fil i källkatalogen. Här är ett exempel på verifieringsrapporten:

![exempel för växling vid fel](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametrar

| Parameter | Beskrivning | Krävs |
| ----- | -----| ----- |
| TemplatePath | Anger sökvägen till rekursivt hitta Azure Resource Manager-mallar | Ja | 
| TemplatePattern | Anger namnet på mallfilerna ska matchas. | Nej |
| CapabilitiesPath | Anger sökvägen till molnet funktioner JSON-fil | Ja | 
| IncludeComputeCapabilities | Innehåller utvärdering av IaaS-resurser som VM-storlekar och VM-tillägg | Nej |
| IncludeStorageCapabilities | Innehåller utvärdering av lagringsresurser som SKU-typer | Nej |
| Rapport | Anger namnet på den genererade HTML-rapporten | Nej |
| Utförlig | Loggar fel och varningar i konsolen | Nej|


### <a name="examples"></a>Exempel
Det här exemplet kontrollerar alla de [Azure Stack-snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) hämtas lokalt och validerar också storlekar på VM-tillägg mot Azure Stack Development Kit funktioner.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```


## <a name="next-steps"></a>Nästa steg
 - [Distribuera mallar till Azure-stacken](azure-stack-arm-templates.md)
 - [Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)

