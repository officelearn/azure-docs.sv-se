---
title: Övervaka integration runtime i Azure Data Factory | Microsoft Docs
description: Lär dig hur du övervakar olika typer av integration runtime i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: douglasl
ms.openlocfilehash: 4f1e6e7e61cdfd72251120f3c03df7a689aecafe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997462"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Övervaka en integration runtime i Azure Data Factory  
**Integreringskörningen** är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla olika funktioner för dataintegrering i olika nätverksmiljöer. Det finns tre typer av integration Runtime som erbjuds av Data Factory:

- Azure Integration Runtime
- Integration Runtime med egen värd
- Azure SSIS Integration Runtime

Kör följande PowerShell-kommando för att hämta status för en instans av integration runtime (IR): 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdleten returnerar olika typer av information för olika typer av integration runtime. Den här artikeln beskriver egenskaper och status för varje typ av integration runtime.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
Beräkningsresurs för en Azure integration runtime är fullständigt hanterad Elastiskt i Azure. I följande tabell innehåller beskrivningar av egenskaper som returneras av den **Get-AzureRmDataFactoryV2IntegrationRuntime** kommando:

### <a name="properties"></a>Egenskaper
Följande tabell innehåller beskrivningar av egenskaper som returneras av cmdlet: en för en Azure integration runtime:

| Egenskap  | Beskrivning |
-------- | ------------- | 
| Namn | Namnet på Azure integration runtime. |  
| Status | Status för Azure integration runtime. | 
| Plats | Platsen för Azure integration runtime. Mer information om platsen för en Azure integration runtime finns [introduktion till integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Namnet på datafabriken som tillhör Azure integration runtime. | 
| ResourceGroupName | Namnet på resursgruppen som datafabriken tillhör.  |
| Beskrivning | Beskrivning av integration runtime.  |

### <a name="status"></a>Status
Följande tabell innehåller olika statusar av en Azure integration runtime:

| Status | Kommentarer/scenarier | 
| ------ | ------------------ |
| Online | Azure integration runtime är online och redo att användas. | 
| Offline | Azure integration runtime är offline på grund av ett internt fel. |

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd
Det här avsnittet innehåller beskrivningar av egenskaper som returneras av cmdleten Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Innehåller information om övergripande lokal integration runtime och varje nod i runtime egenskaperna som returneras och status.  

### <a name="properties"></a>Egenskaper

Följande tabell innehåller beskrivningar av övervakning av egenskaper för **varje nod**:

| Egenskap  | Beskrivning | 
| -------- | ----------- | 
| Namn | Namnet på den lokala integreringskörningen och noder som är kopplade till den. Noden är en lokal Windows-dator som har en lokal integration runtime installerad. |  
| Status | Status för övergripande lokal integration runtime och varje nod. Exempel: Online/Offline/Limited/osv. Information om dessa statusar finns i nästa avsnitt. | 
| Version | Versionen av lokal integration runtime och varje nod. Versionen av den lokala integreringskörningen bestäms baserat på version av merparten av noder i gruppen. Om det finns noder med olika versioner i lokal integration runtime-installationsprogrammet kan egenvärdbaserade endast noderna med samma versionsnummer som den logiska integration runtime funktionen korrekt. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering misslyckas). | 
| Ledigt minne | Tillgängligt minne på en lokal integration runtime-noden. Det här värdet är en nästan i realtid ögonblicksbild. | 
| Processoranvändning | CPU-utnyttjande på en lokal integration runtime-noden. Det här värdet är en nästan i realtid ögonblicksbild. |
| Nätverk (In/ut) | Nätverksanvändningen för en lokal integration runtime-noden. Det här värdet är en nästan i realtid ögonblicksbild. | 
| Samtidiga jobb (körs / begränsa) | **Kör**. Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nästan i realtid ögonblicksbild. <br/><br/>**Gränsen**. Gränsen innebär det att maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på storleken på datorn. Du kan höja gränsen att skala upp samtidiga jobbkörning i avancerade scenarier när tidsgränsen nåddes även när CPU, minne eller nätverket är underutnyttjade för aktiviteter. Den här funktionen är också tillgängliga med en enda nod lokal integration runtime. |
| Roll | Det finns två typer av roller i ett flernodigt lokal integration runtime – dispatcher- och arbetsroller. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-noden som används för att hämta uppgifter/jobb från cloud services och skicka dem till olika arbetsnoder. Dispatcher-noden är också en underordnad nod. |

Vissa inställningar i egenskaperna för att klarna mer när det finns två eller flera noder i en lokal integration runtime (det vill säga i en skalbar scenariot).

#### <a name="concurrent-jobs-limit"></a>Gräns för samtidiga jobb

Standardvärdet för samtidiga jobb anges gränsen baserat på storleken på datorn. De faktorer som används för att beräkna det här värdet beror på mängden RAM-minne och antalet CPU-kärnor för datorn. Fler kärnor och mer minne, desto högre begränsa så standardvärdet av samtidiga jobb.

Du skala ut genom att öka antalet noder. När du ökar antalet noder, är samtidiga jobb gränsen summan av gränsvärdena för samtidiga jobb av alla tillgängliga noder.  Till exempel om en nod kan du köra upp till tolv samtidiga jobb, kan lägger till tre fler liknande noder du köra upp till 48 samtidiga jobb (4 x 12). Vi rekommenderar att du ökar gräns för samtidiga jobb bara när du ser låg Resursanvändning med standardvärdena på varje nod.

Du kan åsidosätta beräknade standardvärdet i Azure-portalen. Välj Skapa > anslutningar > Integreringskörningar > Redigera > noder > ändra värdet för samtidiga jobb per nod. Du kan också använda PowerShell [uppdatering azurermdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/update-azurermdatafactoryv2integrationruntimenode?view=azurermps-6.4.0#examples) kommando.
  
### <a name="status-per-node"></a>Status (per nod)
Följande tabell innehåller olika statusar en lokal integration runtime-noden:

| Status | Beskrivning |
| ------ | ------------------ | 
| Online | Noden är ansluten till Data Factory-tjänsten. |
| Offline | Noden är offline. |
| Uppgraderar | Noden uppdateras automatiskt. |
| Begränsad | På grund av ett problem med anslutningen. Kanske på grund av HTTP-port 8050 problemet, service bus-anslutningsproblem eller ett problem för synkronisering av autentiseringsuppgifter. |
| Inaktiv | Noden är i en konfiguration som skiljer sig från konfigurationen av andra majoritet noder. |

En nod kan vara inaktiv när den inte kan ansluta till andra noder.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (övergripande lokal integration runtime)
Följande tabell innehåller olika statusar av en lokal integration runtime. Denna status är beroende av status för alla noder som tillhör körningen. 

| Status | Beskrivning |
| ------ | ----------- | 
| Behöver registrering | Någon nod är registrerad för den här lokala integreringskörningen ännu. |
| Online | Alla noder är online. |
| Offline | Någon nod är online. |
| Begränsad | Inte alla noder i det här lokal integration runtime är i felfritt tillstånd. Den här statusen är en varning om att vissa noder kanske inte körs. Den här statusen kan bero på en autentiseringsuppgift synkroniseringsproblem på dispatcher-/ arbetsnoden. |

Använd den **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** cmdlet för att hämta JSON-nyttolasten som innehåller detaljerade egenskaper för integration runtime med egen värd och deras ögonblicksbild värden under tiden för körningen av den cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Exempel på utdata (förutsätter att det finns två noder som är associerade med den här lokal integration runtime):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS Integration Runtime
Azure-SSIS integration runtime är ett fullständigt hanterat kluster av Azure virtuella datorer (eller noder) som dedikerade för att köra dina SSIS-paket. Alla andra aktiviteter i Azure Data Factory körs inte. När du har etablerat kan du övervaka dess status för övergripande/nodspecifik fråga efter dess egenskaper.

### <a name="properties"></a>Egenskaper

| Egenskapen/Status | Beskrivning |
| --------------- | ----------- |
| CreateTime | UTC-tid när din Azure-SSIS integration runtime har skapats. |
| Noder | Allokerat/tillgängliga noder på din Azure-SSIS integration runtime med nodspecifik status (startar/tillgängliga/återvinning/är inte tillgänglig) och fel som behöver åtgärdas. |
| OtherErrors | De icke-nod-specifika fel som behöver åtgärdas på din Azure-SSIS integration runtime. |
| LastOperation | Resultatet av åtgärd som senast Starta/Stoppa på din Azure-SSIS integration runtime med användbara fel om den misslyckades. |
| Status | Övergripande status (första/starta/igång/stoppa/stoppad) för din Azure-SSIS integration runtime. |
| Plats | Platsen för din Azure-SSIS integration runtime. |
| NodeSize | Storleken på varje nod i din Azure-SSIS integration runtime. |
| NodeCount | Antalet noder i din Azure-SSIS integration runtime. |
| MaxParallelExecutionsPerNode | Antalet parallella körningar per nod i din Azure-SSIS integration runtime. |
| CatalogServerEndpoint | Slutpunkten för din befintliga Azure SQL Database/hanterad instans-server som värd för SSISDB. |
| CatalogAdminUserName | Administratörens användarnamn för din befintliga Azure SQL Database/hanterad instans-server. Data Factory-tjänsten använder denna information för att förbereda och hantera SSISDB för din räkning. |
| CatalogAdminPassword | Administratörslösenordet för din befintliga Azure SQL Database/hanterad instans-server. |
| CatalogPricingTier | Prisnivå för SSISDB som värd för din befintliga Azure SQL Database-server.  Ej tillämpligt för Azure SQL Database Managed Instance som är värd för SSISDB. |
| VNetId | Det virtuella resource ID för din Azure-SSIS integration runtime att ansluta till. |
| Undernät | Namnet på undernätet för din Azure-SSIS integration runtime kan anslutas till. |
| ID | Resurs-ID för din Azure-SSIS integration runtime. |
| Typ | Typen (hanterade/Self-signed-Hosted) på din Azure-SSIS integration runtime. |
| ResourceGroupName | Namnet på din Azure-resursgrupp som din data factory och Azure-SSIS integration runtime har skapats. |
| DataFactoryName | Namnet på din Azure data factory. |
| Namn | Namnet på din Azure-SSIS integration runtime. |
| Beskrivning | Beskrivning av din Azure-SSIS integration runtime. |

  
### <a name="status-per-node"></a>Status (per nod)

| Status | Beskrivning |
| ------ | ----------- | 
| Startar | Den här noden förbereds. |
| Tillgängligt | Den här noden är redo att distribuera/köra SSIS-paket. |
| Återvinning | Den här noden är att reparera/omstart. |
| Ej tillgänglig | Den här noden är inte redo att distribuera/köra SSIS-paket och användbara fel/problem som du kan lösa. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (övergripande Azure-SSIS integration runtime)

| Övergripande status | Beskrivning | 
| -------------- | ----------- | 
| Inledande | Noderna på din Azure-SSIS integration runtime har inte allokerats/förberett. | 
| Startar | Noderna på din Azure-SSIS integration runtime som ska allokeras/förberedd och fakturering har startats. |
| Startade | Noderna på din Azure-SSIS integration runtime har allokerats/förberedd och de är redo att distribuera/köra SSIS-paket. |
| Stoppas  | Noderna på din Azure-SSIS integration runtime släpps. |
| Stoppad | Noderna på din Azure-SSIS integration runtime har publicerats och fakturering har stoppats. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Övervaka Azure-SSIS integration runtime i Azure portal

Följande skärmbilder visar hur du väljer Azure-SSIS IR att övervaka och ge ett exempel på den information som visas.

![Välj Azure-SSIS integration runtime att övervaka](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Visa information om Azure-SSIS integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Övervaka Azure-SSIS integration runtime med PowerShell

Använd ett skript som i följande exempel för att kontrollera status för Azure-SSIS IR.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mer information om Azure-SSIS integration runtime

Se följande artiklar för att lära dig mer om Azure-SSIS integration runtime:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integreringskörningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL Database Managed Instance och ansluter IR till ett virtuellt nätverk. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Det ger också steg för att konfigurera det virtuella nätverket så att Azure-SSIS IR kan ansluta till det virtuella nätverket med hjälp av Azure portal. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för att övervaka pipelines på olika sätt: 

- [Snabbstart: skapa en datafabrik](quickstart-create-data-factory-dot-net.md).
- [Använda Azure Monitor för att övervaka Data Factory-pipelines](monitor-using-azure-monitor.md)
