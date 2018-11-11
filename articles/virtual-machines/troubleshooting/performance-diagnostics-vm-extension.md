---
title: Prestanda för Azure Diagnostics VM-tillägg för Windows | Microsoft Docs
description: Introducerar Azure prestanda diagnostik VM-tillägg för Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a76b22feee84820f3acc963af70ec24669a216d7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251976"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Prestanda för Azure Diagnostics VM-tillägg för Windows

Azure prestanda diagnostik VM-tillägget kan samla in diagnostikdata för prestanda från Windows-datorer. Tillägget gör analys och tillhandahåller en rapport över resultat och rekommendationer för att identifiera och lösa prestandaproblem på den virtuella datorn. Det här tillägget installeras ett felsökningsverktyg som kallas [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
    > Om du vill köra diagnostik på din virtuella dator från Azure-portalen för icke-klassiska virtuella datorer rekommenderas att använda den nya upplevelsen. Mer information finns i [Prestandadiagnostik för Azure-datorer](performance-diagnostics.md) 

## <a name="prerequisites"></a>Förutsättningar

Det här tillägget kan installeras på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det kan också installeras på Windows 8.1 och Windows 10.

## <a name="extension-schema"></a>Tilläggsschema
Följande JSON visar schemat för Azure VM Diagnostics-tillägget prestanda. Det här tillägget kräver namnet och nyckeln för ett lagringskonto att lagra utdata för diagnostik och rapporten. Dessa värden är känslig. Nyckeln för lagringskontot ska lagras i en skyddad Konfigurationsinställningen. Azure VM-tillägg som skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att **storageAccountName** och **storageAccountKey** är skiftlägeskänsliga. Andra nödvändiga parametrar visas i följande avsnitt.

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
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
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
|utgivare|Microsoft.Azure.Performance.Diagnostics|Publisher-namnområde för tillägget.
|typ|AzurePerformanceDiagnostics|Typ av VM-tillägget.
|typeHandlerVersion|1.0|Versionen av tillägget hanteraren.
|performanceScenario|grundläggande|Prestanda-scenario som du vill samla in data. Giltiga värden är: **grundläggande**, **vmslow**, **azurefiles**, och **anpassade**.
|traceDurationInSeconds|300|Varaktigheten för spårningar, om något av spårningsalternativ för har valts.
|perfCounterTrace|p|Alternativet för att aktivera prestandaräknaren spårningen. Giltiga värden är **p** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som tom.
|networkTrace|N|Alternativet för att aktivera nätverksspårning. Giltiga värden är **n** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som tom.
|xperfTrace|x|Alternativet för att aktivera XPerf spårningen. Giltiga värden är **x** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som tom.
|storPortTrace|S|Alternativet för att aktivera StorPort spårningen. Giltiga värden är **s** eller tomt värde. Om du inte vill samla in den här spårningen lämna värdet som tom.
|srNumber|123452016365929|Biljett supportnumret, om det är tillgängligt. Lämna värdet som tom om du inte har den.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuellt datum tid i Utc. Om du använder portalen för att installera tillägg, behöver du inte ange det här värdet.
|resourceId|/subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /providers/ {resourceProviderNamespace} / {resourceType} / {resourceName}|Den unika identifieraren för en virtuell dator.
|storageAccountName|mystorageaccount|Namnet på lagringskontot för att lagra diagnostikloggar och resultat.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Nyckel för lagringskontot.

## <a name="install-the-extension"></a>Installera tillägget

Följ dessa instruktioner för att installera tillägget på Windows-datorer:

1. Logga in på [Azure Portal](http://portal.azure.com).
2. Välj den virtuella datorn där du vill installera det här tillägget.

    ![Skärmbild av Azure portal med virtuella datorer som är markerat](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Välj den **tillägg** bladet och välj **Lägg till**.

    ![Skärmbild av tillägg bladet med Lägg till markerad](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Välj **Azure Prestandadiagnostik**granska villkoren och markera **skapa**.

    ![Skärmbild av nya resurs-fönster, där Azure Prestandadiagnostik markerat](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Ange parametervärden för installationen och välj **OK** att installera tillägget. Mer information om vilka scenarier som finns i [hur du använder PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Skärmbild av installera tillägget för dialogrutan](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. När installationen har slutförts visas ett meddelande om denna status.

    ![Skärmbild av etablering lyckades meddelande](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Tillägget körs när etableringen har slutförts. Det tar två minuter eller mindre för att slutföra den grundläggande scenario. För andra scenarier körs den via den tid som anges under installationen.

## <a name="remove-the-extension"></a>Ta bort tillägget
Följ dessa steg om du vill ta bort tillägget från en virtuell dator:

1. Logga in på den [Azure-portalen](http://portal.azure.com), Välj den virtuella datorn från vilken du vill ta bort tillägget och välj sedan den **tillägg** bladet. 
2. Välj den (**...** ) för prestanda Diagnostics-tillägg-posten i listan och välj **avinstallera**.

    ![Skärmbild av tillägg bladet avinstalleras markerat](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Du kan också välja tilläggsposten och välja den **avinstallera** alternativet.

## <a name="template-deployment"></a>Malldistribution
Azure virtual machine-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall. Den här lösningen körs tillägget Azure prestanda diagnostik VM under en malldistribution för Azure Resource Manager. Här är en exempelmall:

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
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
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
Den `Set-AzureRmVMExtension` kommando kan användas för att distribuera Azure Diagnostics-tillägget prestanda VM till en befintlig virtuell dator.

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
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

## <a name="information-on-the-data-captured"></a>Information om de data som hämtats
Verktyget PerfInsights samlar in olika loggar, konfiguration och diagnostiska data, beroende på det valda scenariot. Mer information finns i den [PerfInsights dokumentation](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visa och dela resultatet

Utdata från tillägget finns i en zip-fil som laddats upp till det lagringskonto som angetts under installationen och delas i 30 dagar med hjälp av [signaturer för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Den här zipfilen innehåller diagnostikloggar och en rapport med resultat och rekommendationer. En SAS-länk till utdata zip-filen finns i en textfil med namnet *zipfilename*_saslink.txt under mappen **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version >**. Alla som har den här länken kan ladda ned zip-filen.

För att hjälpa supporttekniker som arbetar på ditt supportärende kan Microsoft använda SAS-länk för nedladdning av diagnostikdata.

Du kan visa rapporten genom att extrahera zip-filen och öppna den **PerfInsights Report.html** fil.

Du bör också att kunna hämta zip-filen direkt från portalen genom att välja tillägget.

![Skärmbild av Prestandadiagnostik detaljerad status](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> SAS-länk som visas i portalen kanske inte fungerar ibland. Detta kan orsakas av en felaktig URL under de kodning och avkodning. Du kan hämta en länk i stället direkt från filen *_saslink.txt från den virtuella datorn.

## <a name="troubleshoot-and-support"></a>Felsökning och support

- Distributionsstatus för tillägget (i meddelandeområdet) kan visa ”distribution pågår” trots att tillägget har etablerats.

    Det här problemet kan ignoreras, förutsatt att tilläggets status anger att tillägget har etablerats.
- Du kan lösa vissa problem under installationen genom att använda tillägget loggarna. Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/), och välj **få support**. Information om hur du använder Azure-support finns på [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
