---
title: "Azure prestanda diagnostik VM-tillägget för Windows | Microsoft Docs"
description: "Introducerar Azure prestanda diagnostik VM-tillägget för Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 3e2f8be0f77e220da483dcfb18d6b324d3f203ed
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azure prestanda diagnostik VM-tillägget för Windows

Azure prestanda diagnostik VM-tillägget kan samla in diagnostik prestandadata från virtuella Windows-datorer. Tillägget gör analys och ger en rapport över resultaten och rekommendationerna för att identifiera och lösa problem med prestanda på den virtuella datorn. Det här tillägget installeras ett verktyg för felsökning kallas [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Förutsättningar

Det här tillägget kan installeras på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det kan också installeras på Windows 8.1 och Windows 10.

## <a name="extension-schema"></a>Tilläggsschema
Följande JSON visar schemat för Azure prestanda diagnostik VM-tillägget. Det här tillägget kräver namnet och nyckeln för ett lagringskonto att lagra diagnostik utdata och rapporten. Dessa värden är känslig. Lagringskontonyckel ska lagras i en Inställningskonfiguration för skyddade. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att **storageAccountName** och **storageAccountKey** är skiftlägeskänsliga. Andra nödvändiga parametrar visas i följande avsnitt.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Egenskapsvärden

|   **Namn**   |**Värdet / exempel**|       **Beskrivning**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versionen av API: et.
|publisher|Microsoft.Azure.Performance.Diagnostics|Publisher namnområde för tillägget.
|typ|AzurePerformanceDiagnostics|Typ av VM-tillägget.
|typeHandlerVersion|1.0|Versionen av tillägget hanteraren.
|performanceScenario|grundläggande|Scenario för prestanda som du vill samla in data. Giltiga värden är: **grundläggande**, **vmslow**, **azurefiles**, och **anpassade**.
|traceDurationInSeconds|300|Varaktighet för spårning, om något av alternativen för spårningen har valts.
|perfCounterTrace|p|Alternativet för att aktivera spårning av prestandaräknaren. Giltiga värden är **p** eller tomt värde. Låt värdet som tom om du inte vill samla in den här spårningen.
|networkTrace|n|Alternativet för att aktivera spårning i nätverket. Giltiga värden är **n** eller tomt värde. Låt värdet som tom om du inte vill samla in den här spårningen.
|xperfTrace|x|Alternativet för att aktivera XPerf spårning. Giltiga värden är **x** eller tomt värde. Låt värdet som tom om du inte vill samla in den här spårningen.
|storPortTrace|S|Alternativet för att aktivera spårning för StorPort. Giltiga värden är **s** eller tomt värde. Låt värdet som tom om du inte vill samla in den här spårningen.
|srNumber|123452016365929|Stöd för biljettnumret, om de är tillgängliga. Låt värdet som tom om du inte har den.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuellt datum/tid i Utc. Om du använder portalen för att installera det här tillägget, behöver du inte ange det här värdet.
|storageAccountName|mystorageaccount|Namnet på lagringskontot för att lagra diagnostik loggar och resultat.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Nyckel för lagringskontot.

## <a name="install-the-extension"></a>Installera tillägget

Följ instruktionerna för att installera tillägget på virtuella Windows-datorer:

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Välj den virtuella datorn där du vill installera det här tillägget.

    ![Skärmbild av Azure-portalen med virtuella datorer som är markerat](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Välj den **tillägg** , och välj **Lägg till**.

    ![Skärmbild av tillägg bladet med Lägg till markerat](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Välj **Azure Prestandadiagnostik**granskar du de allmänna villkoren och välj **skapa**.

    ![Skärmbild av ny resurs-skärmen, med Azure Prestandadiagnostik markerat](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Ange parametervärden för installationen och välj **OK** att installera tillägget. Mer information om scenarier som stöds finns [hur du använder PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Skärmbild av installera tillägget dialogrutan](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. När installationen har slutförts visas ett meddelande som anger den här statusen.

    ![Skärmbild av etablering lyckades meddelande](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Tillägget körs när etableringen har slutförts. Det tar två minuter eller mindre för att slutföra för grundläggande scenariot. För andra scenarier körs via den tid som anges under installationen.

## <a name="remove-the-extension"></a>Ta bort tillägget
Följ dessa steg för att ta bort tillägget från en virtuell dator:

1. Logga in på den [Azure-portalen](http://portal.azure.com), Välj den virtuella dator som du vill ta bort det här tillägget och välj sedan den **tillägg** bladet. 
2. Välj den (**...** ) för posten prestanda diagnostik tillägg i listan och välj **avinstallera**.

    ![Skärmbild av tillägg bladet avinstalleras markerat](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Du kan också välja tilläggsposten och välja den **avinstallera** alternativet.

## <a name="template-deployment"></a>Malldistribution
Tillägg för virtuell dator i Azure kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall. Detta kör Azure prestanda diagnostik VM-tillägget under en Azure Resource Manager för malldistribution. Här är en exempelmall:

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {            
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell-distribution
Den `Set-AzureRmVMExtension` kommando kan användas för att distribuera Azure prestanda diagnostik VM-tillägget till en befintlig virtuell dator.

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
````

## <a name="information-on-the-data-captured"></a>Information om data som hämtats
Verktyget PerfInsights samlar in olika loggar, konfiguration och diagnostiska data, beroende på det valda scenariot. Mer information finns i [PerfInsights dokumentationen](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visa och dela resultaten

Utdata från tillägget kan hittas i en zip-fil som överförs till lagringskontot angav vid installationen och delas i 30 dagar med hjälp av [signaturer av delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Den här filen innehåller diagnostikloggar och en rapport med resultaten och rekommendationerna. En SAS-länk till utdata zip-filen kan finnas i en textfil med namnet *zipfilename*_saslink.txt under mappen **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version >**. Alla som har den här länken kan hämta zip-filen.

För att underlätta supportteknikern arbetar på ditt supportärende kan Microsoft använda SAS länken för att hämta diagnostikdata.

Om du vill visa rapporten extrahera zip-filen och öppna den **PerfInsights Report.html** fil.

Du bör även kunna hämta zip-filen direkt från portalen genom att markera tillägget.

![Skärmbild av Prestandadiagnostik detaljerad status](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> SAS-länken som visas i portalen kanske inte fungerar ibland. Detta kan orsakas av en felaktig URL under åtgärder för kodning och avkodning. Du kan hämta en länk i stället direkt från filen *_saslink.txt från den virtuella datorn.

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

- Tillägget Distributionsstatus (i meddelandefältet) kan visa ”distribution pågår” även om tillägget har etablerats.

    Det här problemet kan ignoreras, förutsatt att tillståndets status anger att tillägget har etablerats.
- Du kan lösa problem under installationen genom att använda tillägget loggarna. Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/), och välj **få support**. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
