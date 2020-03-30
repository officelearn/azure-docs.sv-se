---
title: Installera och konfigurera Windows Azure diagnostics-tillägg (WAD)
description: Lär dig hur du samlar in Azure-diagnostikdata i ett Azure Storage-konto så att du kan visa dem med ett av flera tillgängliga verktyg.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672267"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installera och konfigurera Windows Azure diagnostics-tillägg (WAD)
Azure Diagnostics-tillägget är en agent i Azure Monitor som samlar in övervakningsdata från gästoperativsystemet och arbetsbelastningar för virtuella Azure-datorer och andra beräkningsresurser. Den här artikeln innehåller information om hur du installerar och konfigurerar Windows-diagnostiktillägget och en beskrivning av hur data lagras i och Azure Storage-konto.

Diagnostiktillägget implementeras som ett [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md) i Azure, så det stöder samma installationsalternativ med Hjälp av Resource Manager-mallar, PowerShell och CLI. Mer information om hur du installerar och underhåller tillägg för virtuella datorer finns i Tillägg för virtuella datorer och [funktioner för Windows.](../../virtual-machines/extensions/features-windows.md)

## <a name="install-with-azure-portal"></a>Installera med Azure-portal
Du kan installera och konfigurera diagnostiktillägget på en enskild virtuell dator i Azure-portalen som ger dig ett gränssnitt i stället för att arbeta direkt med konfigurationen. När du aktiverar diagnostiktillägget används automatiskt en standardkonfiguration med de vanligaste prestandaräknarna och händelserna. Du kan ändra den här standardkonfigurationen enligt dina specifika krav.

> [!NOTE]
> Det finns inställningar för diagnostiktillägg som du inte kan konfigurera med azure-portalen, inklusive att skicka data till Azure Event Hubs. Du måste använda någon av de andra konfigurationsmetoderna för dessa inställningar.

1. Öppna menyn för en virtuell dator i Azure-portalen.
2. Klicka på **Diagnostikinställningar** i avsnittet **Övervakning** på VM-menyn.
3. Klicka på **Aktivera övervakning på gästnivå** om diagnostiktillägget inte redan har aktiverats.
4. Ett nytt Azure Storage-konto skapas för den virtuella datorn med namnet kommer att baseras på namnet på resursgruppen för den virtuella datorn. Du kan koppla den virtuella datorn till ett annat lagringskonto genom att välja fliken **Agent.**

![Diagnostikinställningar](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Du kan ändra standardkonfigurationen när diagnostiktillägget har aktiverats. I följande tabell beskrivs de alternativ som du kan ändra på de olika flikarna. Vissa alternativ har ett **anpassat** kommando som gör att du kan ange mer detaljerad konfiguration. Mer information om olika inställningar finns i [Windows-schema för diagnostiktillägg.](diagnostics-extension-schema-windows.md)

| Flik | Beskrivning |
|:---|:---|
| Översikt | Visar den aktuella konfigurationen med länkar till de andra flikarna. |
| Prestandaräknare | Välj prestandaräknare som ska samlas in och samplingsfrekvensen för varje.  |
| Loggar | Välj de loggdata som ska samlas in. Detta inkluderar Windows-händelseloggar, IIS-loggar, .NET-programloggar och ETW-händelser.  |
| Kraschdumpar | Aktivera kraschdump för olika processer. |
| Sjunker | Aktivera datamottagare för att skicka data till destinationer utöver Azure Storage.<br>Azure Monitor - Skickar prestandadata till Azure Monitor-mått.<br>Application Insights - Skicka data till ett program för application insights. |
| Agent | Ändra följande konfiguration för agenten:<br>- Ändra lagringskontot.<br>- Ange den maximala lokala disken som används för agenten.<br>- Konfigurera loggar för hälsan hos själva agenten.|


> [!NOTE]
> Konfigurationen för diagnostiktillägg kan formateras i antingen JSON eller XML, men alla konfigurationer som görs i Azure-portalen lagras alltid som JSON. Om du använder XML med en annan konfigurationsmetod och sedan ändrar konfigurationen med Azure-portalen ändras inställningarna till JSON.

## <a name="resource-manager-template"></a>Resource Manager-mall
Se [Använda övervakning och diagnostik med mallar för Windows VM och Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) om hur du distribuerar diagnostiktillägget med Azure Resource Manager-mallar. 

## <a name="azure-cli-deployment"></a>Azure CLI-distribution
Azure CLI kan användas för att distribuera Azure Diagnostics-tillägget till en befintlig virtuell dator med [az vm-tillägg som anges](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) som i följande exempel. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De skyddade inställningarna definieras i [privateconfig-elementet](diagnostics-extension-schema-windows.md#privateconfig-element) i konfigurationsschemat. Följande är ett minimalt exempel på en skyddad inställningsfil som definierar lagringskontot. Se [Exempelkonfiguration](diagnostics-extension-schema-windows.md#privateconfig-element) för fullständig information om de privata inställningarna.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
De offentliga inställningarna definieras i [det offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurationsschemat. Följande är ett minimalt exempel på en offentlig inställningsfil som möjliggör insamling av diagnostikinfrastrukturloggar, en enda prestandaräknare och en enda händelselogg. Se [Exempelkonfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de offentliga inställningarna.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell-distribution
PowerShell kan användas för att distribuera Azure Diagnostics-tillägget till en befintlig virtuell dator med [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) som i följande exempel. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De privata inställningarna definieras i [PrivateConfig-elementet,](diagnostics-extension-schema-windows.md#privateconfig-element)medan de offentliga inställningarna definieras i [det offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurationsschemat. Du kan också välja att ange information om lagringskontot som parametrar för cmdlet set-azvmdiagnosticsextension i stället för att inkludera dem i de privata inställningarna.

Följande är ett minimalt exempel på en konfigurationsfil som möjliggör insamling av diagnostikinfrastrukturloggar, en enda prestandaräknare och en enda händelselogg. Se [Exempelkonfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de privata och offentliga inställningarna. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Se även [Använda PowerShell för att aktivera Azure Diagnostics på en virtuell dator som kör Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Datalagring
I följande tabell visas de olika typerna av data som samlas in från diagnostiktillägget och om de lagras som en tabell eller en blob. De data som lagras i tabeller kan också lagras i blobbar beroende på [StorageType-inställningen](diagnostics-extension-schema-windows.md#publicconfig-element) i din offentliga konfiguration.


| Data | Lagringstyp | Beskrivning |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabell | Diagnostiska övervakare och konfigurationsändringar. |
| WADDirectoriesTable | Tabell | Kataloger som diagnostikövervakaren övervakar.  Detta inkluderar IIS-loggar, IIS-misslyckade begärandeloggar och anpassade kataloger.  Platsen för blob-loggfilen anges i fältet Behållare och namnet på blobben finns i fältet RelativPath.  AbsolutePath-fältet anger platsen och namnet på filen som den fanns på den virtuella Azure-datorn. |
| WadLogsTable | Tabell | Loggar inskrivna med hjälp av spårningslyssnaren. |
| WADPerformanceCountersTable | Tabell | Prestandaräknare. |
| WADWindowsEventLogsTable | Tabell | Windows-händelseloggar. |
| wad-iis-failedreqlogfiles | Blob | Innehåller information från loggar för misslyckade begäranden i IIS. |
| wad-iis-logfiler | Blob | Innehåller information om IIS-loggar. |
| "anpassade" | Blob | En anpassad behållare som baseras på konfigurera kataloger som övervakas av diagnostikövervakaren.  Namnet på den här blob-behållaren anges i WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att visa diagnostikdata
Flera verktyg finns tillgängliga för att visa data när de har överförts till lagring. Ett exempel:

* Serverutforskaren i Visual Studio – Om du har installerat Azure Tools för Microsoft Visual Studio kan du använda noden Azure Storage i Server Explorer för att visa skrivskyddade blob- och tabelldata från dina Azure-lagringskonton. Du kan visa data från ditt lokala lagringsemulatorkonto och även från lagringskonton som du har skapat för Azure. Mer information finns i [Bläddra och hantera lagringsresurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör att du enkelt kan arbeta med Azure Storage-data på Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure Diagnostics Manager som låter dig visa, ladda ned och hantera diagnostikdata som samlas in av programmen som körs på Azure.

## <a name="next-steps"></a>Efterföljande moment
- Mer information om hur du vidarebefordrar övervakningsdata till Azure Event Hubs finns i [Skicka data från Windows Azure-diagnostiktillägg till eventhubbar.](diagnostics-extension-stream-event-hubs.md)
