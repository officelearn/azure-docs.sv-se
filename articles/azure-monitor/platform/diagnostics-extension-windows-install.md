---
title: Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)
description: Lär dig hur du samlar in Azure Diagnostics-data i ett Azure Storage-konto så att du kan visa det med ett av flera tillgängliga verktyg.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672267"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)
Tillägget Azure Diagnostics är en agent i Azure Monitor som samlar in övervaknings data från gäst operativ systemet och arbets belastningar på virtuella Azure-datorer och andra beräknings resurser. Den här artikeln innehåller information om hur du installerar och konfigurerar Windows Diagnostics-tillägget och en beskrivning av hur data lagras i och Azure Storage konto.

Tillägget för diagnostik implementeras som ett [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md) i Azure, så det stöder samma installations alternativ med Resource Manager-mallar, POWERSHELL och CLI. Se [tillägg och funktioner för virtuella datorer för Windows](../../virtual-machines/extensions/features-windows.md) om du vill ha mer information om hur du installerar och hanterar tillägg för virtuella datorer.

## <a name="install-with-azure-portal"></a>Installera med Azure Portal
Du kan installera och Konfigurera diagnostik-tillägget på en enskild virtuell dator i Azure Portal, vilket ger dig ett gränssnitt i stället för att arbeta direkt med konfigurationen. När du aktiverar Diagnostics-tillägget använder det automatiskt en standard konfiguration med de vanligaste prestanda räknarna och-händelserna. Du kan ändra den här standard konfigurationen utifrån dina egna krav.

> [!NOTE]
> Det finns inställningar för diagnostikinställningar som du inte kan konfigurera med hjälp av Azure Portal, inklusive att skicka data till Azure Event Hubs. Du måste använda någon av de andra konfigurations metoderna för de här inställningarna.

1. Öppna menyn för en virtuell dator i Azure Portal.
2. Klicka på **diagnostikinställningar** i avsnittet **övervakning** på menyn VM.
3. Klicka på **Aktivera övervakning på gästnivå** om inget diagnostik-tillägg redan har Aktiver ATS.
4. Ett nytt Azure Storage-konto skapas för den virtuella datorn med namnet baserat på namnet på resurs gruppen för den virtuella datorn. Du kan koppla den virtuella datorn till ett annat lagrings konto genom att välja fliken **agent** .

![Diagnostikinställningar](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Du kan ändra standard konfigurationen när diagnostik-tillägget har Aktiver ATS. I följande tabell beskrivs de alternativ som du kan ändra på olika flikar. Vissa alternativ har ett **anpassat** kommando som gör att du kan ange mer detaljerad konfiguration. Se [Windows diagnosticss tilläggs schema](diagnostics-extension-schema-windows.md) för information om olika inställningar.

| Tabbtecken | Beskrivning |
|:---|:---|
| Översikt | Visar den aktuella konfigurationen med länkar till de andra flikarna. |
| Prestandaräknare | Välj de prestanda räknare som ska samlas in och samplings frekvensen för var och en.  |
| Loggar | Välj de loggdata som ska samlas in. Detta inkluderar händelse loggar i Windows, IIS-loggar, .NET-program loggar och ETW-händelser.  |
| Kraschdumpar | Aktivera kraschdump för olika processer. |
| Mottagare | Aktivera data mottagare för att skicka data till destinationer, förutom Azure Storage.<br>Azure Monitor-skickar prestanda data till Azure Monitor mått.<br>Application Insights – skicka data till ett Application Insights-program. |
| Agent | Ändra följande konfiguration för agenten:<br>-Ändra lagrings kontot.<br>-Ange den maximala lokala disken som används för agenten.<br>– Konfigurera loggar för själva agentens hälso tillstånd.|


> [!NOTE]
> Även om konfigurationen för Diagnostics-tillägget kan formateras i JSON eller XML kommer alla konfigurationer som görs i Azure Portal alltid att lagras som JSON. Om du använder XML med en annan konfigurations metod och sedan ändrar konfigurationen med Azure Portal, ändras inställningarna till JSON.

## <a name="resource-manager-template"></a>Resource Manager-mall
Se [använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar](../../virtual-machines/extensions/diagnostics-template.md) för distribution av diagnostikprogrammet med Azure Resource Manager mallar. 

## <a name="azure-cli-deployment"></a>Azure CLI-distribution
Azure CLI kan användas för att distribuera Azure-diagnostik-tillägget till en befintlig virtuell dator som använder [AZ VM-tillägg](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) som i följande exempel. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De skyddade inställningarna definieras i PrivateConfig- [elementet](diagnostics-extension-schema-windows.md#privateconfig-element) för konfigurations schemat. Följande är ett minimalt exempel på en skyddad inställnings fil som definierar lagrings kontot. Se [exempel på konfiguration](diagnostics-extension-schema-windows.md#privateconfig-element) för fullständig information om de privata inställningarna.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
De offentliga inställningarna definieras i det [offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurations schemat. Följande är ett minimalt exempel på en fil med en offentlig inställning som möjliggör insamling av diagnostiska infrastruktur loggar, en enda prestanda räknare och en enda händelse logg. Se [exempel på konfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de offentliga inställningarna.

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
PowerShell kan användas för att distribuera Azure-diagnostik-tillägget till en befintlig virtuell dator med [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) som i följande exempel. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De privata inställningarna definieras i PrivateConfig- [elementet](diagnostics-extension-schema-windows.md#privateconfig-element), medan de offentliga inställningarna definieras i det [offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurations schemat. Du kan också välja att ange information om lagrings kontot som parametrar för cmdleten Set-AzVMDiagnosticsExtension i stället för att inkludera dem i de privata inställningarna.

Följande är ett minimalt exempel på en konfigurations fil som möjliggör insamling av diagnostiska infrastruktur loggar, en enda prestanda räknare och en enda händelse logg. Se [exempel på konfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de privata och offentliga inställningarna. 

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

Se även [använda PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Datalagring
I följande tabell visas olika typer av data som har samlats in från diagnostikprogrammet och om de lagras som en tabell eller en blob. Data som lagras i tabeller kan också lagras i blobbar beroende på [inställningen StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) i din offentliga konfiguration.


| Data | Lagringstyp | Beskrivning |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabell | Diagnostisk övervakning och konfigurations ändringar. |
| WADDirectoriesTable | Tabell | Kataloger som diagnostiken övervakar.  Detta inkluderar IIS-loggar, IIS misslyckade begär Anden och anpassade kataloger.  Platsen för blobb logg filen anges i fältet container och namnet på blobben är i fältet RelativePath.  Fältet AbsolutePath anger platsen och namnet på filen som den fanns på den virtuella Azure-datorn. |
| WadLogsTable | Tabell | Loggar som skrivits i kod med spårnings lyssnaren. |
| WADPerformanceCountersTable | Tabell | Prestanda räknare. |
| WADWindowsEventLogsTable | Tabell | Händelse loggar i Windows. |
| wad-IIS-failedreqlogfiles | Blob | Innehåller information från IIS-begäranden om misslyckade förfrågningar. |
| wad-IIS-loggfiler | Blob | Innehåller information om IIS-loggar. |
| bild | Blob | En anpassad behållare baserat på hur du konfigurerar kataloger som övervakas av diagnostisk övervakare.  Namnet på den här BLOB-behållaren anges i WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att Visa diagnostikdata
Det finns flera verktyg för att visa data när de har överförts till lagring. Några exempel:

* Server Explorer i Visual Studio – om du har installerat Azure-verktygen för Microsoft Visual Studio kan du använda noden Azure Storage i Server Explorer för att visa skrivskyddade blob-och tabell data från dina Azure Storage-konton. Du kan visa data från ditt lokala Storage emulator-konto och även från lagrings konton som du har skapat för Azure. Mer information finns i avsnittet [om att bläddra och hantera lagrings resurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure-diagnostik Manager som gör att du kan visa, ladda ned och hantera de diagnostikdata som samlas in av programmen som körs på Azure.

## <a name="next-steps"></a>Nästa steg
- Se [skicka data från Windows Azure Diagnostics-tillägget till Event Hubs](diagnostics-extension-stream-event-hubs.md) för information om vidarebefordran av övervaknings data till Azure Event Hubs.
