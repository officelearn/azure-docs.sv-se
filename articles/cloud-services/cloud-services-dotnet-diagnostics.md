---
title: Hur du använder Azure diagnostics (.NET) med molntjänster | Microsoft Docs
description: Med Azure-diagnostik för att samla in data från Azure-molntjänster för felsökning, mäta prestanda, övervakning, trafik analys och mer.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: adegeo
ms.openlocfilehash: a8d6b16fa363062e06d48bfc5af2ca37697d5cd8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
ms.locfileid: "29460907"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Aktivera Azure-diagnostik i Azure-molntjänster
Se [översikt över Azure Diagnostics](../azure-diagnostics.md) för en bakgrund på Azure-diagnostik.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Så här aktiverar du diagnostik i en Arbetsroll
Här beskrivs hur du implementerar en Azure-arbetsroll som skickar telemetridata med hjälp av .NET EventSource-klassen. Azure-diagnostik för att samla in telemetridata och lagrar den i ett Azure storage-konto. När du skapar en arbetsroll kan Visual Studio automatiskt diagnostik 1.0 som en del av lösningen i Azure SDK för .NET 2.4 och tidigare. I följande anvisningar beskrivs processen för att skapa arbetsrollen inaktivera diagnostik 1.0 från lösningen, och distribuera diagnostik 1.2 eller 1.3 worker-rollen.

### <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har en Azure-prenumeration och använder Visual Studio med Azure SDK. Om du inte har en Azure-prenumeration kan du registrera dig för den [kostnadsfri utvärderingsversion][Free Trial]. Se till att [installera och konfigurera Azure PowerShell version 0.8.7 eller senare][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Steg 1: Skapa en Arbetsroll
1. Starta **Visual Studio**.
2. Skapa en **Azure Cloud Service** projektet från den **moln** mall som riktar sig till .NET Framework 4.5.  Namnge projektet ”WadExample” och klicka på Ok.
3. Välj **Arbetsrollen** och klicka på Ok. Projektet kommer att skapas.
4. I **Solution Explorer**, dubbelklicka på den **WorkerRole1** egenskaper för filen.
5. I den **Configuration** fliken avmarkera **aktivera diagnostik** att inaktivera diagnostik 1.0 (Azure SDK 2.4 och tidigare versioner).
6. Skapa en lösning för att kontrollera att du har några fel.

### <a name="step-2-instrument-your-code"></a>Steg 2: Instrumentera koden
Ersätt innehållet i WorkerRole.cs med följande kod. Klass SampleEventSourceWriter, som ärvts från det [EventSource klassen][EventSource Class], implementerar fyra metoderna för loggning: **SendEnums**, **MessageMethod** , **SetOther** och **HighFreq**. Den första parametern till den **WriteEvent** metod definierar ID för respektive händelsen. Metoden implementerar en oändlig loop som anropar metoderna för loggning som implementerats i den **SampleEventSourceWriter** klassen var 10: e sekund.

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
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Steg 3: Distribuera Arbetsrollen

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Distribuera arbetsrollen till Azure från Visual Studio genom att välja den **WadExample** projektet i Solution Explorer sedan **publicera** från den **skapa** menyn.
2. Välj din prenumeration.
3. I den **Publiceringsinställningar i Microsoft Azure** markerar **Skapa nytt...** .
4. I den **skapa Molntjänsten och Lagringskontot** dialogrutan, ange en **namn** (till exempel ”WadExample”) och välj en region eller affinitetsgrupp.
5. Ange den **miljö** till **mellanlagring**.
6. Ändra andra **inställningar** som behövs och klicka på **publicera**.
7. När distributionen har slutförts, verifiera i Azure-portalen som din molntjänst i en **kör** tillstånd.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Steg 4: Skapa konfigurationsfilen diagnostik och installera tillägget
1. Hämta schemadefinitionen offentliga konfiguration filen genom att köra följande PowerShell-kommando:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Lägg till en XML-fil till din **WorkerRole1** projektet genom att högerklicka på den **WorkerRole1** projektet och välj **Lägg till** -> **nytt objekt...** -> **Visual C# objekt** -> **Data** -> **XML-filen**. Namn på filen ”WadExample.xml”.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Koppla WadConfig.xsd till konfigurationsfilen. Kontrollera att WadExample.xml editor-fönstret är det aktiva fönstret. Tryck på **F4** att öppna den **egenskaper** fönster. Klicka på den **scheman** egenskap i den **egenskaper** fönster. Klicka på den **...** i den **scheman** egenskapen. Klicka på **Lägg …** och navigera till den plats där du sparade XSD-filen och välj filen WadConfig.xsd. Klicka på **OK**.

4. Ersätt innehållet i konfigurationsfilen WadExample.xml med följande XML och spara filen. Den här konfigurationsfilen definierar några prestandaräknare för att samla in: en för CPU-användning och en för minnesanvändning. Konfigurationen definierar de fyra händelser som motsvarar metoderna i klassen SampleEventSourceWriter.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Steg 5: Installera diagnostik på Worker-rollen
PowerShell-cmdletar för att hantera diagnostik för en webb- eller arbetarroll roll är: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension och ta bort AzureServiceDiagnosticsExtension.

1. Öppna Azure PowerShell.
2. Kör skriptet för att installera diagnostik på worker-rollen (Ersätt *StorageAccountKey* med lagringskontonyckel lagringskontots wadexample och *config_path* med sökvägen till  *WadExample.xml* filen):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Steg 6: Visa telemetridata
I Visual Studio **Server Explorer**, navigera till wadexample storage-konto. När Molntjänsten har körts cirka fem (5) minuter, bör du se tabellerna **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** och **WADSetOtherTable**. Dubbelklicka på en av tabellerna för att visa telemetri som har samlats in.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schemat för konfigurationsfilen
Konfigurationsfilen diagnostik definierar värden som används för att initiera diagnostiska konfigurationsinställningar när diagnostik agenten startas. Finns det [senaste Schemareferens](https://msdn.microsoft.com/library/azure/mt634524.aspx) giltiga värden och exempel.

## <a name="troubleshooting"></a>Felsökning
Om du har problem med att se [felsökning Azure Diagnostics](../azure-diagnostics-troubleshooting.md) för hjälp med vanliga problem.

## <a name="next-steps"></a>Nästa steg
[Visa en lista över relaterade Azure virtuella datorer diagnostiska artiklar](../monitoring-and-diagnostics/azure-diagnostics.md#cloud-services-using-azure-diagnostics) felsökning om du vill ändra de data du samlar in eller Läs mer om diagnostik i allmänhet.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
