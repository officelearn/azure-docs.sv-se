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
ms.openlocfilehash: 85d4764534c77ea0e4d999e249abe456d0234d75
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azure prestanda diagnostik VM-tillägget för Windows

## <a name="summary"></a>Sammanfattning
Azure prestanda diagnostik VM-tillägget hjälper samlar in diagnostik prestandadata från virtuella Windows-datorer, utför analys och ger en rapport över resultaten och rekommendationerna för att identifiera och lösa problem med prestanda på den virtuella datorn. Det här tillägget installeras ett verktyg för felsökning kallas [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Krav
### <a name="operating-systems"></a>Operativsystem
Det här tillägget kan installeras på Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 och Windows 10-operativsystem.

## <a name="extension-schema"></a>Tilläggsschema
Följande JSON visar schemat för Azure prestanda diagnostik tillägget. Det här tillägget kräver namn och en lagringsbehållare till lagring diagnostik utdata och rapporten. Värdena är känsliga och ska lagras i skyddade Inställningskonfiguration. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att storageAccountName och storageAccountKey är skiftlägeskänsliga. Andra nödvändiga parametrar finns i avsnittet nedan.

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
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Egenskapsvärden

|   **Namn**   |**Värdet / exempel**|       **Beskrivning**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versionen av API: et
|Publisher|Microsoft.Azure.Performance.Diagnostics|Publisher namnområdet för tillägget
|typ|AzurePerformanceDiagnostics|Typ av VM-tillägget
|typeHandlerVersion|1.0|Versionen av tillägget-hanteraren
|performanceScenario|Grundläggande|Prestanda scenario för att samla in data för. Giltiga värden är: **grundläggande**, **vmslow**, **azurefiles**, och **anpassade**.
|traceDurationInSeconds|300|Varaktighet för spåren om något av alternativen för spårningen har valts.
|DiagnosticsTrace|D|Alternativet för att aktivera diagnostiska spårning. Giltiga värden är **d** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som anges.
|perfCounterTrace|P|Alternativet för att aktivera spårning av prestandaräknaren. Giltiga värden är **p** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som anges.
|networkTrace|n|Alternativet för att aktivera Netmon spårning. Giltiga värden är  **n**  eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som anges.
|xperfTrace|x|Alternativet för att aktivera XPerf spårning. Giltiga värden är **x** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som anges.
|storPortTrace|S|Alternativet för att aktivera spårning för StorPort. Giltiga värden är s eller ett tomt värde. Om du inte vill samla in den här spårningen lämna värdet som anges.
|srNumber|123452016365929|Om det finns stöd för Biljettnummer. Lämna som tomt om du inte har den.
|requestTimeUtc|2017-9/2 11:06:00 PM|Aktuellt datum/tid i Utc. Du behöver inte ange det här värdet om du använder portalen för att installera det här tillägget.
|storageAccountName|mittlagringskonto|Namnet på lagringskontot för att lagra diagnostik loggar och resultat.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc ==|Nyckel för lagringskontot.

## <a name="install-the-extension"></a>Installera tillägget

Följ dessa steg om du vill installera tillägg för virtuell dator på virtuella Windows-datorer:

1. Logga in på [Azure Portal](http://portal.azure.com).
2. Välj den virtuella datorn där du vill installera det här tillägget.

    ![Välj den virtuella datorn](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Välj **tillägg** bladet och klicka på den **Lägg till** knappen.

    ![Välj Tillägg](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Välj Azure prestanda diagnostik tillägg, granska de allmänna villkoren och klicka på den **skapa** knappen.

    ![Skapa Azure prestanda diagnostik tillägg](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Ange parametervärden för installation och klicka på **OK** att installera tillägget. Du hittar mer information om scenarierna som stöds felsökning [här](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Installera tillägget](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. När installationen har slutförts visas ett meddelande om etablering har slutförts.

    ![Etableringen har slutförts meddelande](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Tillägget körningen startar när etableringen är klar och det tar några minuter eller mindre för att slutföra körningen för grundläggande scenario. För andra scenarier körs via den tid som anges under installationen.

## <a name="remove-the-extension"></a>Ta bort tillägget
Följ dessa steg för att ta bort tillägget från en virtuell dator:

1. Logga in på den [Azure-portalen](http://portal.azure.com), Välj den virtuella datorn där du vill ta bort det här tillägget och välj sedan tillägg bladet. 
2. Klicka på den (**...** ) för prestanda diagnostik tillägget posten i listan och väljer avinstallera.

    ![Avinstallera tillägget](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Du kan också markera tilläggsposten och väljer alternativet för avinstallera.

## <a name="template-deployment"></a>Malldistribution
Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för Azure-diagnostik för prestanda under en Azure Resource Manager för malldistribution. Här är en exempelmall som kan användas med malldistribution:

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
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
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
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell-distribution
Den `Set-AzureRmVMExtension` kommando kan användas för att distribuera Azure-diagnostik för prestanda-tillägget för virtuell dator till en befintlig virtuell dator. Innan du kör kommandot måste de offentliga och privata konfigurationerna lagras i en PowerShell-hash-tabell.

PowerShell

````
$PublicSettings = @{ "performanceScenario" = "basic"; "traceDurationInSeconds" = 300; "diagnosticsTrace" = "d"; "perfCounterTrace" = "p"; "networkTrace" = ""; "xperfTrace" = ""; "storPortTrace" = ""; "srNumber" = ""; "requestTimeUtc" = "2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName" = "mystorageaccount" ; "storageAccountKey" = "mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Information om data som hämtats
PerfInsights verktyget samlar in olika loggar, konfiguration, diagnostikdata etc. beroende på det valda scenariot. För mer information om data som samlas in per scenario du besök [PerfInsights dokumentationen](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visa och dela resultaten

Utdata för tillägget lagras i en mapp med namnet log_collection under Temp enhet (vanligtvis D:\log_collection) som standard. Du kan se zip-fil som innehåller diagnostiska loggar och en rapport med resultaten och rekommendationerna under den här mappen.

Zip-filen skapas också överföras till storage-konto som angavs under installationen och delas i 30 dagar med [signaturer av delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). En textfil med namnet *zipfilename*_saslink.txt skapas också i mappen log_collection. Den här filen innehåller SAS-länk skapas för att hämta zip-filen. Alla som har den här länken kommer att kunna hämta zip-filen.

Microsoft använder SAS länken för att hämta data för ytterligare undersökning av stöd för tekniker som arbetar på ditt supportärende diagnostik.

Om du vill visa rapporten bara extrahera zip-filen och öppna **PerfInsights Report.html** fil.

Du kanske även kan hämta zip-filen direkt från portalen genom att markera tillägget.

![Detaljerad status](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> SAS-länken som visas i portalen kanske inte fungerar ibland på grund av felaktig Url (orsakas av specialtecken) under kodning och avkodning igen. Lösningen är att hämta en länk direkt från filen *_saslink.txt från den virtuella datorn.

## <a name="troubleshoot-and-support"></a>Felsöka och stöd
### <a name="troubleshoot"></a>Felsöka

- Tillägget Distributionsstatus (i meddelandefältet) kan visa ”distribution pågår” även om tillägget har etablerats.

    Det här problemet kan ignoreras så länge tilläggets status anger att tillägget har etablerats.
- Vissa problem under installationen kan du felsöka med tillägget loggar. Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
