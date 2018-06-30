---
title: Använda blob storage för IIS- och lagring för händelser i Azure Log Analytics | Microsoft Docs
description: Logganalys kan läsa loggfiler för Azure-tjänster som skriver diagnostik till tabellagring eller IIS-loggar som skrivs till blob storage.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 8f923cc081ea652c8e32d4109225044c70c8767d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128749"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Använda Azure blob storage för IIS och Azure-tabellagring för händelser med logganalys

Logganalys kan läsa loggfiler för följande tjänster som skriver diagnostik till tabellagring eller IIS-loggar som skrivs till blob storage:

* Service Fabric-kluster (förhandsgranskning)
* Virtuella datorer
* Web/Worker-roller

Innan logganalys kan samla in data för dessa resurser, måste Azure diagnostics aktiveras.

När diagnostik är aktiverade, kan du använda Azure portal eller PowerShell konfigurera Log Analytics för att samla in loggarna.

Azure Diagnostics är en Azure-tillägget som gör det möjligt att samla in diagnostikdata från arbetsrollen, webbroll eller virtuell dator som körs i Azure. Data lagras i ett Azure storage-konto och sedan ska samlas in av logganalys.

Log Analytics att samla in loggarna Azure-diagnostik måste loggarna ha följande platser:

| Loggtyp | Resurstyp | Plats |
| --- | --- | --- |
| IIS-loggar |Virtuella datorer <br> Webbroller <br> Worker-roller |bomullstuss-iis-loggfiler (Blob Storage) |
| Syslog |Virtuella datorer |LinuxsyslogVer2v0 (tabell lagring) |
| Service Fabric operativa händelser |Service Fabric-noder |WADServiceFabricSystemEventTable |
| Service Fabric tillförlitliga aktören händelser |Service Fabric-noder |WADServiceFabricReliableActorEventTable |
| Händelser för Service Fabric tillförlitlig tjänst |Service Fabric-noder |WADServiceFabricReliableServiceEventTable |
| Windows-händelseloggar |Service Fabric-noder <br> Virtuella datorer <br> Webbroller <br> Worker-roller |WADWindowsEventLogsTable (Table Storage) |
| ETW-Windows-loggar |Service Fabric-noder <br> Virtuella datorer <br> Webbroller <br> Worker-roller |WADETWEventTable (Table Storage) |

> [!NOTE]
> IIS-loggar från Azure-webbplatser stöds inte för närvarande.
>
>

För virtuella datorer, har du möjlighet att installera den [logganalys agent](log-analytics-azure-vm-extension.md) till den virtuella datorn att aktivera ytterligare insikter. Förutom att analysera IIS-loggar och händelseloggar kan utföra du ytterligare analys, inklusive konfiguration ändringsspårning, SQL-bedömning och utvärdering av uppdateringar.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Aktivera Azure-diagnostik i en virtuell dator för insamling av webbloggar händelseloggen och IIS
Använd följande procedur för att aktivera Azure-diagnostik i en virtuell dator för händelseloggen och IIS Logginsamling med hjälp av Microsoft Azure-portalen.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Så här aktiverar du Azure-diagnostik i en virtuell dator med Azure-portalen
1. Installera den Virtuella Datoragenten när du skapar en virtuell dator. Om den virtuella datorn redan finns kontrollerar du att den Virtuella Datoragenten är installerad.

   * I Azure portal, navigerar du till den virtuella datorn, Välj **valfri konfiguration**, sedan **diagnostik** och ange **Status** till **på** .

     Den virtuella datorn har filnamnstillägget Azure Diagnostics installerade och körs när åtgärden har slutförts. Det här tillägget är ansvarig för att samla in diagnostikdata.
2. Aktivera övervakning och konfigurera händelseloggning på en befintlig virtuell dator. Du kan aktivera diagnostik på VM-nivå. Om du vill aktivera diagnostik och sedan konfigurera händelseloggning, utför du följande steg:

   1. Välj VM.
   2. Klicka på **övervakning**.
   3. Klicka på **diagnostik**.
   4. Ange den **Status** till **på**.
   5. Markera varje diagnostik-logg som du vill samla in.
   6. Klicka på **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Aktivera Azure-diagnostik i en webbroll för IIS-loggen och händelsen samling
Referera till [hur att aktivera diagnostik i en molntjänst](../cloud-services/cloud-services-dotnet-diagnostics.md) allmänna anvisningar om hur du aktiverar Azure-diagnostik. Anvisningarna nedan använder den här informationen och anpassa den för användning med logganalys.

Med Azure diagnostics aktiverad:

* IIS-loggar lagras som standard med loggdata överförs vid intervallet som scheduledTransferPeriod överföring.
* Windows-händelseloggar överförs inte som standard.

### <a name="to-enable-diagnostics"></a>Aktivera diagnostik
Aktivera Windows-händelseloggar, eller ändra scheduledTransferPeriod, konfigurera Azure-diagnostik med XML-konfigurationsfilen (diagnostics.wadcfg) enligt [steg 4: skapa konfigurationsfilen diagnostik och installera tillägget](../cloud-services/cloud-services-dotnet-diagnostics.md)

Följande exempel konfigurationsfil samlar in IIS-loggar och alla händelser från program- och systemloggarna:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Se till att din ConfigurationSettings anger ett lagringskonto, som i följande exempel:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Den **AccountName** och **AccountKey** värden finns i Azure-portalen på instrumentpanelen för storage-konto, under hantera åtkomstnycklar. Protokollet för anslutningssträngen måste vara **https**.

När den uppdaterade diagnostiska konfigurationen tillämpas på Molntjänsten och det skriver diagnostik till Azure Storage, är du redo att konfigurera logganalys.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Använda Azure portal för att samla in loggar från Azure Storage
Du kan använda Azure-portalen för att konfigurera Log Analytics för att samla in loggar för följande Azure-tjänster:

* Service Fabric-kluster
* Virtuella datorer
* Web/Worker-roller

Navigera till logganalys-arbetsytan i Azure-portalen och utföra följande uppgifter:

1. Klicka på *lagringskonton loggar*
2. Klicka på den *Lägg till* aktivitet
3. Välj lagringskonto som innehåller diagnostik-loggar
   * Det här kontot kan vara ett klassiska storage-konto eller ett lagringskonto i Azure Resource Manager
4. Välj en datatyp som du vill samla in loggar för
   * Alternativen är IIS-loggar. Händelser. Syslog (Linux) ETW-loggar. Service Fabric-händelser
5. Värdet för källa fylls i automatiskt baserat på datatyp och kan inte ändras
6. Klicka på OK om du vill spara konfigurationen

Upprepa steg 2 till 6 för ytterligare lagringskonton och datatyper som du vill använda Log Analytics för att samla in.

Du ska kunna se data från storage-konto i logganalys cirka 30 minuter. Data som skrivs till lagring när konfigurationen tillämpas visas bara. Logganalys läser inte befintliga data från lagringskontot.

> [!NOTE]
> Portalen kontrollerar inte att källan finns i lagringskontot eller om nya data skrivs.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Aktivera Azure-diagnostik i en virtuell dator för händelseloggen och IIS logg med PowerShell
Följ stegen i [konfigurera logganalys att indexera Azure diagnostics](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) du använder PowerShell för att läsa från Azure-diagnostik som skrivs till table storage.

Med hjälp av Azure PowerShell kan du mer exakt ange de händelser som skrivs till Azure Storage.
Mer information finns i [aktiverar diagnostik i Azure Virtual Machines](../virtual-machines-dotnet-diagnostics.md).

Du kan aktivera och uppdatera Azure diagnostics med följande PowerShell-skript.
Du kan också använda det här skriptet till en konfiguration för anpassad loggning.
Ändra skriptet för att ange storage-konto, tjänstnamn och namn på virtuell dator.
Skriptet använder cmdlets för klassiska virtuella datorer.

Granska följande skriptexempel, kopierar den, ändra det efter behov, spara exemplet som en PowerShell-skriptfil och kör skriptet.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Nästa steg
* [Samla in loggar och mått för Azure-tjänster](log-analytics-azure-storage.md) för Azure-tjänster som stöds.
* [Aktivera lösningar](log-analytics-add-solutions.md) att ge insikt om data.
* [Använd sökfrågor](log-analytics-log-searches.md) att analysera data.
