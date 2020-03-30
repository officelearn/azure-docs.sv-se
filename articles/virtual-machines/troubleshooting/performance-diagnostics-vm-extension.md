---
title: Vm-tillägg för Azure Performance Diagnostics för Windows| Microsoft-dokument
description: Introducerar Azure Performance Diagnostics VM-tillägg för Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057549"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azures VM-tillägg för prestandadiagnostik för Windows

Azure Performance Diagnostics VM Extension hjälper till att samla in prestandadiagnostiska data från virtuella windows-datorer. Tillägget utför analys och innehåller en rapport med resultat och rekommendationer för att identifiera och lösa prestandaproblem på den virtuella datorn. Det här tillägget installerar ett felsökningsverktyg som heter [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Om du vill köra diagnostik på den virtuella datorn från Azure-portalen för icke-klassiska virtuella datorer rekommenderar vi att du använder den nya upplevelsen. Mer information finns i [Prestandadiagnostik för virtuella Azure-datorer](performance-diagnostics.md) 

## <a name="prerequisites"></a>Krav

Det här tillägget kan installeras på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det kan också installeras på Windows 8.1 och Windows 10.

## <a name="extension-schema"></a>Tilläggsschema
Följande JSON visar schemat för VM-tillägg för Azure Performance Diagnostics. Det här tillägget kräver namn och nyckel för ett lagringskonto för att lagra diagnostikutdata och rapport. Dessa värden är känsliga. Lagringskontonyckeln bör lagras i en skyddad inställningskonfiguration. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att **storageAccountName** och **storageAccountKey** är skiftlägeskänsliga. Andra obligatoriska parametrar visas i följande avsnitt.

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

|   **Namn**   |**Värde / Exempel**|       **Beskrivning**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versionen av API: et.
|utgivare|Microsoft.Azure.Performance.Diagnostik|Utgivarens namnområde för tillägget.
|typ|AzurePerformanceDiagnostik|Typ av vm-tillägg.
|typHandlerVersion|1.0|Versionen av förlängningshanteraren.
|prestandaScenario|Grundläggande|Prestandascenariot för vilka data ska samlas in. Giltiga värden är: **grundläggande,** **vmslow,** **azurefiles**och **anpassade**.
|traceDurationInSeconds|300|Varaktigheten av spårningarna, om något av spårningsalternativen är markerat.
|perfCounterTrace|P|Alternativ för att aktivera Prestandaräknarespårning. Giltiga värden är **p** eller tomt värde. Om du inte vill fånga den här spårningen lämnar du värdet som tomt.
|nätverkTrace|n|Alternativ för att aktivera Nätverksspårning. Giltiga värden är **n** eller tomt värde. Om du inte vill fånga den här spårningen lämnar du värdet som tomt.
|xperfTrace (på en)|x|Alternativ för att aktivera XPerf Trace. Giltiga värden är **x** eller tomt värde. Om du inte vill fånga den här spårningen lämnar du värdet som tomt.
|storPortTrace|s|Alternativ för att aktivera StorPort Trace. Giltiga värden är **s** eller tomt värde. Om du inte vill fånga den här spårningen lämnar du värdet som tomt.
|srNumber (srNumber)|123452016365929|Supportbiljettnumret, om tillgängligt. Lämna värdet tomt om du inte har det.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuell datumtid i Utc. Om du använder portalen för att installera det här tillägget behöver du inte ange det här värdet.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Den unika identifieraren för en virtuell dator.
|storageAccountName|mystorageaccount|Namnet på lagringskontot för att lagra diagnostikloggar och resultat.
|lagringSförståe att|lDuVvxuZB28NNP... hAiRF3voADxLBTcc==|Nyckeln för lagringskontot.

## <a name="install-the-extension"></a>Installera tillägget

Så här installerar du tillägget på virtuella Datorer i Windows:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn där du vill installera det här tillägget.

    ![Skärmbild av Azure-portalen, med virtuella datorer markerade](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Välj **Bladet Tillägg** och välj **Lägg till**.

    ![Skärmbild av blad för tillägg, med Lägg till markerat](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Välj **Azure Performance Diagnostics**, granska villkoren och välj **Skapa**.

    ![Skärmbild av ny resursskärm med Azure Performance Diagnostics markerat](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Ange parametervärdena för installationen och välj **OK** för att installera tillägget. Mer information om scenarier som stöds finns i [Så här använder du PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Skärmbild av dialogrutan Installera tillägg](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. När installationen lyckas visas ett meddelande som anger den här statusen.

    ![Skärmbild av meddelandet Etablerande](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Tillägget körs när etableringen har lyckats. Det tar två minuter eller mindre att slutföra för det grundläggande scenariot. För andra scenarier körs den genom den varaktighet som angavs under installationen.

## <a name="remove-the-extension"></a>Ta bort tillägget
Så här tar du bort tillägget från en virtuell dator:

1. Logga in på [Azure-portalen,](https://portal.azure.com)välj den virtuella datorn som du vill ta bort det här tillägget från och välj sedan bladet **Tillägg.** 
2. Välj posten (**...**) för tillägget Tillägg för prestandadiagnostik i listan och välj **Avinstallera**.

    ![Skärmbild av blad för tillägg, med Avinstallera markerat](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Du kan också välja tilläggsposten och välja alternativet **Avinstallera.**

## <a name="template-deployment"></a>Malldistribution
Azure-tillägg för virtuella datorer kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall. Detta kör Azure Performance Diagnostics VM-tillägget under en Azure Resource Manager-malldistribution. Här är en exempelmall:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
```

## <a name="powershell-deployment"></a>PowerShell-distribution
Kommandot `Set-AzVMExtension` kan användas för att distribuera AZURE Performance Diagnostics VM-tillägg till en befintlig virtuell dator.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Information om de infångade uppgifterna
PerfInsights-verktyget samlar in olika loggar, konfigurations- och diagnostikdata, beroende på det valda scenariot. Mer information finns i [Dokumentationen för PerfInsights](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visa och dela resultaten

Utdata från tillägget finns i en zip-fil som laddades upp till det lagringskonto som angetts under installationen och delas i 30 dagar med hjälp av [SAS (Shared Access Signatures).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) Denna zip-fil innehåller diagnostiska loggar och en rapport med resultat och rekommendationer. En SAS-länk till zip-filen för utdata finns i en textfil med namnet *zipfilename*_saslink.txt under mappen **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<version>**. Alla som har denna länk kan ladda ner zip-filen.

För att hjälpa supportteknikern som arbetar med supportbiljetten kan Microsoft använda den här SAS-länken för att hämta diagnostikdata.

Om du vill visa rapporten extraherar du zip-filen och öppnar filen **PerfInsights Report.html.**

Du bör också kunna ladda ner zip-filen direkt från portalen genom att välja tillägget.

![Skärmbild av detaljerad status för prestandadiagnostik](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> SAS-länken som visas i portalen kanske inte fungerar ibland. Detta kan orsakas av en felaktig URL under kodnings- och avkodningsåtgärderna. Du kan istället hämta länken direkt från *_saslink.txt-filen från den virtuella datorn.

## <a name="troubleshoot-and-support"></a>Felsöka och support

- Distributionsstatus för tillägg (i meddelandefältet) kan visa "Pågående distribution" även om tillägget har etablerats.

    Det här problemet kan ignoreras på ett säkert sätt, så länge tilläggsstatusen anger att tillägget har etablerats.
- Du kan åtgärda vissa problem under installationen med hjälp av tilläggsloggarna. Utdata för tilläggskörning loggas till filer som finns i följande katalog:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Hämta support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
