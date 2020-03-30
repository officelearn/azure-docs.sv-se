---
title: Så här använder du Azure Diagnostics (.NET) med Molntjänster | Microsoft-dokument
description: Använda Azure-diagnostik för att samla in data från Azure cloud Services för felsökning, mätning av prestanda, övervakning, trafikanalys med mera.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: 1e49a0935a70a2470267e5458fa1f55e3059e965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469773"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Aktivera Azure Diagnostics i Azure Cloud Services
Se [Azure Diagnostics Overview](../azure-diagnostics.md) för en bakgrund på Azure Diagnostics.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Aktivera diagnostik i en arbetarroll
Den här genomgången beskriver hur du implementerar en Azure-arbetsroll som avger telemetridata med klassen .NET EventSource. Azure Diagnostics används för att samla in telemetridata och lagra dem i ett Azure-lagringskonto. När du skapar en arbetsroll aktiverar Visual Studio automatiskt Diagnostik 1.0 som en del av lösningen i Azure SDK:er för .NET 2.4 och tidigare. Följande instruktioner beskriver processen för att skapa arbetarrollen, inaktivera diagnostik 1.0 från lösningen och distribuera diagnostik 1.2 eller 1.3 till din arbetarroll.

### <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har en Azure-prenumeration och använder Visual Studio med Azure SDK. Om du inte har en Azure-prenumeration kan du registrera dig för den [kostnadsfria utvärderingsversionen][Free Trial]. Se till att [installera och konfigurera Azure PowerShell version 0.8.7 eller senare][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Steg 1: Skapa en arbetarroll
1. Starta **Visual Studio**.
2. Skapa ett **Azure Cloud Service-projekt** från **molnmallen** som är inriktad på .NET Framework 4.5.  Namnge projektet "WadExample" och klicka på Ok.
3. Välj **Arbetarroll** och klicka på Ok. Projektet kommer att skapas.
4. Dubbelklicka på egenskapsfilen **WorkerRole1** i **Solution Explorer.**
5. Avmarkera Aktivera **diagnostik** på fliken **Konfiguration** om du vill inaktivera Diagnostik 1.0 (Azure SDK 2.4 och tidigare).
6. Skapa din lösning för att kontrollera att du inte har några fel.

### <a name="step-2-instrument-your-code"></a>Steg 2: Instrumentera din kod
Ersätt innehållet i WorkerRole.cs med följande kod. Klassen SampleEventSourceWriter, som ärvts från [klassen EventSource,][EventSource Class]implementerar fyra loggningsmetoder: **SendEnums**, **MessageMethod**, **SetOther** och **HighFreq**. Den första parametern till **metoden WriteEvent** definierar ID för respektive händelse. Metoden Kör implementerar en oändlig loop som anropar var och en av de loggningsmetoder som implementeras i klassen **SampleEventSourceWriter** var 10:e sekund.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Steg 3: Distribuera din arbetarroll

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Distribuera din arbetarroll till Azure från Visual Studio genom att välja **WadExample-projektet** i Solution Explorer **och publicera** sedan på **Bygg-menyn.**
2. Välj din prenumeration.
3. I dialogrutan **Microsoft Azure-publiceringsinställningar** väljer du **Skapa ny...**.
4. I dialogrutan **Skapa molntjänst och lagringskonto** anger du ett **namn** (till exempel "WadExample") och väljer en region- eller tillhörighetsgrupp.
5. Ställ in **miljön** **på förproduktion**.
6. Ändra andra **inställningar** efter behov och klicka på **Publicera**.
7. När distributionen har slutförts kontrollerar du i **Running** Azure-portalen att molntjänsten är i körtillstånd.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Steg 4: Skapa konfigurationsfilen för diagnostik och installera tillägget
1. Hämta den offentliga konfigurationsfilschemadefinitionen genom att köra följande PowerShell-kommando:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Lägg till en XML-fil i **Projektet WorkerRole1** genom att högerklicka på **WorkerRole1-projektet** och välja **Lägg till** -> **nytt objekt...** -> **Visual C#-objekt** -> **Data** -> **XML-fil**. Namnge filen "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associera WadConfig.xsd med konfigurationsfilen. Kontrollera att du måste använda redigeringsfönstret WadExample.xml i det aktiva fönstret. Tryck på **F4** för att öppna fönstret **Egenskaper.** Klicka på egenskapen **Scheman** i fönstret **Egenskaper.** Klicka på **...** i egenskapen **Schemas.** Klicka på **Lägg …** och navigera till den plats där du sparade XSD-filen och väljer filen WadConfig.xsd. Klicka på **OK**.

4. Ersätt innehållet i konfigurationsfilen WadExample.xml med följande XML och spara filen. Den här konfigurationsfilen definierar ett par prestandaräknare att samla in: en för CPU-användning och en för minnesanvändning. Därefter definierar konfigurationen de fyra händelser som motsvarar metoderna i klassen SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Steg 5: Installera diagnostik på din arbetarroll
PowerShell-cmdlets för hantering av diagnostik på en webb- eller arbetarroll är: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension och Remove-AzureServiceDiagnosticsExtension.

1. Öppna Azure PowerShell.
2. Kör skriptet för att installera diagnostik på din arbetsroll (ersätt *StorageAccountKey* med lagringskontonyckeln för ditt wadexample-lagringskonto och *config_path* med sökvägen till filen *WadExample.xml):*

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Steg 6: Titta på dina telemetridata
I Visual Studio **Server Explorer**navigerar du till lagringskontot för wadexample. När molntjänsten har körts ungefär fem (5) minuter bör du se tabellerna **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** och **WADSetOtherTable**. Dubbelklicka på en av tabellerna för att visa den telemetri som har samlats in.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schema för konfigurationsfil
Konfigurationsfilen diagnostik definierar värden som används för att initiera inställningar för diagnostikkonfiguration när diagnostikagenten startar. Se den [senaste schemareferensen](/azure/azure-monitor/platform/diagnostics-extension-schema) för giltiga värden och exempel.

## <a name="troubleshooting"></a>Felsökning
Om du har problem läser [du Felsöka Azure Diagnostics](../azure-diagnostics-troubleshooting.md) för hjälp med vanliga problem.

## <a name="next-steps"></a>Efterföljande moment
[Se en lista över relaterade diagnostikartiklar för virtuella Azure-datorer](../azure-monitor/platform/diagnostics-extension-overview.md) för att ändra de data du samlar in, felsöka problem eller lära dig mer om diagnostik i allmänhet.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



