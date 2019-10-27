---
title: Använd Blob Storage för IIS och tabell lagring för händelser i Azure Monitor | Microsoft Docs
description: Azure Monitor kan läsa loggar för Azure-tjänster som skriver diagnostik till Table Storage eller IIS-loggar som skrivs till Blob Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932680"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Samla in Azure-diagnostikloggar från Azure Storage

Azure Monitor kan läsa loggarna för följande tjänster som skriver diagnostik till Table Storage eller IIS-loggar som skrivs till Blob Storage:

* Service Fabric kluster (förhands granskning)
* Virtual Machines
* Webb-/arbets roller

Innan Azure Monitor kan samla in data i en Log Analytics arbets yta för de här resurserna måste Azure Diagnostics vara aktiverat.

När diagnostik har Aktiver ATS kan du använda Azure Portal eller PowerShell konfigurera arbets ytan för att samla in loggarna.

Azure-diagnostik är ett Azure-tillägg som gör att du kan samla in diagnostikdata från en arbets roll, en webb roll eller en virtuell dator som körs i Azure. Data lagras i ett Azure Storage-konto och kan sedan samlas in av Azure Monitor.

För att Azure Monitor ska kunna samla in dessa Azure-diagnostik loggar måste loggarna finnas på följande platser:

| Loggtyp | Resurstyp | Plats |
| --- | --- | --- |
| IIS-loggar |Virtual Machines <br> Webb roller <br> Arbets roller |wad-IIS-loggfiler (Blob Storage) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric operativa händelser |Service Fabric noder |WADServiceFabricSystemEventTable |
| Service Fabric pålitliga aktörs händelser |Service Fabric noder |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable service Events |Service Fabric noder |WADServiceFabricReliableServiceEventTable |
| Händelse loggar i Windows |Service Fabric noder <br> Virtual Machines <br> Webb roller <br> Arbets roller |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW-loggar |Service Fabric noder <br> Virtual Machines <br> Webb roller <br> Arbets roller |WADETWEventTable (Table Storage) |

> [!NOTE]
> IIS-loggar från Azure Websites stöds inte för närvarande.
>
>

För virtuella datorer har du möjlighet att installera [Log Analytics agenten](../../azure-monitor/learn/quick-collect-azurevm.md) i den virtuella datorn för att aktivera ytterligare insikter. Förutom att kunna analysera IIS-loggar och händelse loggar kan du utföra ytterligare analyser, inklusive konfigurations ändrings spårning, SQL-utvärdering och uppdaterings utvärdering.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Aktivera Azure Diagnostics i en virtuell dator för händelse logg och IIS-logg insamling

Använd följande procedur för att aktivera Azure Diagnostics i en virtuell dator för händelse logg och IIS-logg insamling med hjälp av Microsoft Azure-portalen.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Så här aktiverar du Azure Diagnostics på en virtuell dator med Azure Portal

1. Installera VM-agenten när du skapar en virtuell dator. Om den virtuella datorn redan finns kontrollerar du att VM-agenten redan är installerad.

   * I Azure Portal navigerar du till den virtuella datorn, väljer **valfri konfiguration**, sedan **diagnostik** och ställer in **status** på **på**.

     När den virtuella datorn har slutförts har Azure-diagnostik tillägget installerat och körs. Det här tillägget ansvarar för insamling av diagnostikdata.
2. Aktivera övervakning och konfiguration av händelse loggning på en befintlig virtuell dator. Du kan aktivera diagnostik på VM-nivå. Utför följande steg för att aktivera diagnostik och sedan konfigurera händelse loggning:

   1. Välj VM.
   2. Klicka på **övervakning**.
   3. Klicka på **diagnostik**.
   4. Ställ in **statusen** **på på**.
   5. Välj varje diagnos logg som du vill samla in.
   6. Klicka på **OK**

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Aktivera Azure Diagnostics i en webbroll för IIS-logg-och händelse insamling

Se [hur du aktiverar diagnostik i en moln tjänst](../../cloud-services/cloud-services-dotnet-diagnostics.md) för allmänna steg om hur du aktiverar Azure Diagnostics. Anvisningarna nedan använder den här informationen och anpassar den för användning med Log Analytics.

Med Azure Diagnostics aktiverat:

* IIS-loggar lagras som standard, med loggdata som överförs enligt scheduledTransferPeriod-överförings intervallet.
* Windows händelse loggar överförs inte som standard.

### <a name="to-enable-diagnostics"></a>Aktivera diagnostik

Om du vill aktivera Windows-händelseloggar eller ändra scheduledTransferPeriod konfigurerar du Azure-diagnostik med XML-konfigurationsfilen (Diagnostics. wadcfg), som du ser i [steg 4: skapa en konfigurations fil för diagnostik och installera tillägget](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Följande exempel på en konfigurations fil samlar in IIS-loggar och alla händelser från program-och system loggarna:

```xml
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

Se till att din ConfigurationSettings anger ett lagrings konto, som i följande exempel:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Värdena **AccountName** och **AccountKey** finns i Azure Portal på instrument panelen för lagrings kontot under hantera åtkomst nycklar. Protokollet för anslutnings strängen måste vara **https**.

När den uppdaterade diagnostiken har tillämpats på din moln tjänst och skrivning av diagnostik till Azure Storage, är du redo att konfigurera Log Analytics arbets ytan.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Använd Azure Portal för att samla in loggar från Azure Storage

Du kan använda Azure Portal för att konfigurera en Log Analytics arbets yta i Azure Monitor för att samla in loggar för följande Azure-tjänster:

* Service Fabric kluster
* Virtual Machines
* Webb-/arbets roller

Gå till arbets ytan Log Analytics i Azure Portal och utför följande uppgifter:

1. Klicka på *lagrings konto loggar*
2. Klicka på *Lägg till* uppgift
3. Välj det lagrings konto som innehåller diagnostikloggar
   * Detta konto kan vara antingen ett klassiskt lagrings konto eller ett Azure Resource Manager lagrings konto
4. Välj den datatyp som du vill samla in loggar för
   * Alternativen är IIS-loggar. Planering Syslog (Linux); ETW-loggar; Service Fabric händelser
5. Värdet för källan fylls i automatiskt baserat på data typen och kan inte ändras
6. Spara konfigurationen genom att klicka på OK

Upprepa steg 2-6 för ytterligare lagrings konton och data typer som du vill samla in i arbets ytan.

På cirka 30 minuter kan du se data från lagrings kontot i Log Analytics-arbetsytan. Du kan bara se data som skrivs till lagring när konfigurationen har tillämpats. Arbets ytan läser inte befintliga data från lagrings kontot.

> [!NOTE]
> Portalen verifierar inte att källan finns i lagrings kontot eller om nya data skrivs.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Aktivera Azure Diagnostics i en virtuell dator för händelse logg och IIS-logg insamling med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Använd stegen i [konfigurera Azure Monitor för att indexera Azure Diagnostics](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) för att använda PowerShell för att läsa från Azure Diagnostics som skrivs till Table Storage.

Med Azure PowerShell kan du exakt ange de händelser som skrivs till Azure Storage.
Mer information finns i [Aktivera diagnostik i Azure Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Du kan aktivera och uppdatera Azure Diagnostics med följande PowerShell-skript.
Du kan också använda det här skriptet med en anpassad loggnings konfiguration.
Ändra skriptet för att ange lagrings kontot, tjänst namnet och namnet på den virtuella datorn.
Skriptet använder cmdlets för klassiska virtuella datorer.

Granska följande skript exempel, kopiera det, ändra det efter behov, spara exemplet som en PowerShell-skriptfil och kör skriptet.

```powershell
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

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Nästa steg

* [Samla in loggar och mät värden för Azure-tjänster](collect-azure-metrics-logs.md) för Azure-tjänster som stöds.
* [Aktivera lösningar](../../azure-monitor/insights/solutions.md) för att ge inblick i data.
* [Använd Sök frågor](../../azure-monitor/log-query/log-query-overview.md) för att analysera data.