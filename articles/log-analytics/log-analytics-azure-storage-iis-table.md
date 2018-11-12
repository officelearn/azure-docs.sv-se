---
title: Använda blob storage för IIS- och table storage för händelser i Azure Log Analytics | Microsoft Docs
description: Log Analytics kan läsa loggar för Azure-tjänster som skriva diagnostik till tabellagring eller IIS-loggar som skrivs till blob-lagring.
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
ms.component: ''
ms.openlocfilehash: 83c7862e8a9e3ce069765f81607bfbbeee272be2
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278658"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Använda Azure blob storage för IIS och Azure table storage för händelser med Log Analytics

Log Analytics kan läsa loggar för följande tjänster som skriva diagnostik till tabellagring eller IIS-loggar som skrivs till blob-lagring:

* Service Fabric-kluster (förhandsgranskning)
* Virtuella datorer
* Web/Worker-roller

Innan Log Analytics kan samla in data för dessa resurser, måste Azure-diagnostik aktiveras.

När diagnostik är aktiverat kan du kan använda Azure-portalen eller PowerShell konfigurera Log Analytics för att samla in loggarna.

Azure-diagnostik är en Azure-tillägg som hjälper dig att samla in diagnostikdata från en arbetsroll, webbroll eller virtuell dator som kör i Azure. Data lagras i ett Azure storage-konto och sedan ska samlas in av Log Analytics.

Att samla in dessa Azure-diagnostikloggar för logganalys, finnas loggarna på följande platser:

| Loggtyp | Resurstyp | Plats |
| --- | --- | --- |
| IIS-loggar |Virtuella datorer <br> Web-roller <br> Worker-roller |wad-iis-loggfiler (Blob Storage) |
| Syslog |Virtuella datorer |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric operativa händelser |Service Fabric-noder |WADServiceFabricSystemEventTable |
| Service Fabric tillförlitliga aktörer-händelser |Service Fabric-noder |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Services-händelser |Service Fabric-noder |WADServiceFabricReliableServiceEventTable |
| Windows-händelseloggar |Service Fabric-noder <br> Virtuella datorer <br> Web-roller <br> Worker-roller |WADWindowsEventLogsTable (tabellagring) |
| Windows ETW-loggar |Service Fabric-noder <br> Virtuella datorer <br> Web-roller <br> Worker-roller |WADETWEventTable (tabellagring) |

> [!NOTE]
> IIS-loggar från Azure webbplatser stöds inte för närvarande.
>
>

För virtuella datorer, har du möjlighet att installera den [Log Analytics-agenten](log-analytics-quick-collect-azurevm.md) till din virtuella dator för att aktivera ytterligare insikter. Förutom att kunna analysera IIS-loggar och händelseloggar, kan du utföra ytterligare analys, inklusive konfiguration av ändringsspårning, SQL-bedömning och kontroll av uppdateringar.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Aktivera Azure-diagnostik på en virtuell dator för händelseloggen och IIS logga samling
Du kan använda följande procedur för att aktivera Azure-diagnostik i en virtuell dator för händelseloggen och IIS Logginsamling med hjälp av Microsoft Azure-portalen.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Aktivera Azure-diagnostik på en virtuell dator med Azure portal
1. Installera VM-agenten när du skapar en virtuell dator. Om den virtuella datorn redan, kontrollerar du att VM-agenten är redan installerad.

   * I Azure-portalen går du till den virtuella datorn, väljer **valfri konfiguration**, sedan **diagnostik** och ange **Status** till **på** .

     Den virtuella datorn har Azure Diagnostics-tillägget installerat och igång när åtgärden har slutförts. Det här tillägget är ansvarig för att samla in diagnostikdata.
2. Aktivera övervakning och konfigurera händelseloggning i en befintlig virtuell dator. Du kan aktivera diagnostik på VM-nivå. Om du vill aktivera diagnostik och sedan konfigurera händelseloggning, utför du följande steg:

   1. Välj VM.
   2. Klicka på **övervakning**.
   3. Klicka på **diagnostik**.
   4. Ange den **Status** till **på**.
   5. Välj varje diagnostiklogg som du vill samla in.
   6. Klicka på **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Aktivera Azure-diagnostik i en webbroll för IIS-logg- och händelsedata samling
Referera till [hur att aktivera diagnostik i en molntjänst](../cloud-services/cloud-services-dotnet-diagnostics.md) allmänna anvisningar om hur du aktiverar Azure-diagnostik. Anvisningarna nedan använder den här informationen och anpassa den för användning med Log Analytics.

Med Azure-diagnostik aktiverat:

* IIS-loggar lagras som standard med loggdata överförs vid tider som scheduledTransferPeriod överföring.
* Windows-händelseloggar överförs inte som standard.

### <a name="to-enable-diagnostics"></a>Aktivera diagnostik
Aktivera Windows-händelseloggar eller ändra scheduledTransferPeriod, konfigurera Azure Diagnostics med XML-konfigurationsfilen (diagnostics.wadcfg), som visas i [steg 4: skapa konfigurationsfilen diagnostik och installera tillägget](../cloud-services/cloud-services-dotnet-diagnostics.md)

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

Se till att din ConfigurationSettings anger ett storage-konto, som i följande exempel:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Den **AccountName** och **AccountKey** värden finns i Azure-portalen i exempelinstrumentpanelen storage-konto under hantera åtkomstnycklar. Protokollet för anslutningssträngen måste vara **https**.

När den uppdaterade diagnostiska konfigurationen tillämpas i din molntjänst och skrivs diagnostik till Azure Storage, är du redo att konfigurera Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Använd Azure portal för att samla in loggar från Azure Storage
Du kan använda Azure-portalen för att konfigurera Log Analytics för att samla in loggar för följande Azure-tjänster:

* Service Fabric-kluster
* Virtuella datorer
* Web/Worker-roller

Navigera till Log Analytics-arbetsytan i Azure-portalen och utföra följande uppgifter:

1. Klicka på *lagringskontologgar*
2. Klicka på den *Lägg till* uppgift
3. Välj det lagringskonto som innehåller diagnostikloggar
   * Det här kontot kan vara ett klassiskt lagringskonto eller en Azure Resource Manager-lagringskonto
4. Ange den datatyp som du vill samla in loggar för
   * Alternativen är IIS-loggar. Händelserna. Syslog (Linux) ETW-loggar. Service Fabric-händelser
5. Värdet för källa fylls i automatiskt baserat på datatyp och kan inte ändras
6. Klicka på OK om du vill spara konfigurationen

Upprepa steg 2 till 6 för ytterligare lagringskonton och datatyper som du vill använda Log Analytics för att samla in.

Du ska kunna visa data från storage-konto i Log Analytics i cirka 30 minuter. Du ser bara data som skrivs till storage när konfigurationen används. Log Analytics läser inte befintliga data från storage-kontot.

> [!NOTE]
> Portalen kan inte valideras att källan finns i storage-kontot eller om nya data skrivs.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Aktivera Azure-diagnostik på en virtuell dator för händelseloggen och IIS logga samlingen med hjälp av PowerShell
Följ stegen i [konfigurerar Log Analytics för att indexera Azure-diagnostik](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) du använder PowerShell för att läsa från Azure-diagnostik som skrivs till table storage.

Med Azure PowerShell kan du mer exakt ange de händelser som skrivs till Azure Storage.
Mer information finns i [hur du aktiverar diagnostik i Azure Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Du kan aktivera och uppdatera Azure-diagnostik med hjälp av följande PowerShell-skript.
Du kan också använda det här skriptet med en konfiguration för anpassad loggning.
Ändra skriptet för att ange storage-konto, tjänstnamn och namn på virtuell dator.
Skriptet använder cmdletar för klassiska virtuella datorer.

Granska följande skriptexempel, kopiera, ändra det efter behov, spara exemplet som en PowerShell-skriptfil och kör sedan skriptet.

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
* [Aktivera lösningar](../monitoring/monitoring-solutions.md) att ge insikter i data.
* [Använda sökfrågor](log-analytics-queries.md) att analysera data.
