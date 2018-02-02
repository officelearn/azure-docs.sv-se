---
title: "Övervaka integrering körning i Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du övervakar olika typer av integration körning i Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: spelluru
ms.openlocfilehash: b243115eef7e59279fbb1df2a3e3c288477a5b8c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Övervaka en integration körning i Azure Data Factory  
**Integration runtime** är beräkningsinfrastrukturen som används av Azure Data Factory för att ange olika funktioner för integration av data mellan olika nätverksmiljöer. Det finns tre typer av integration körningar som erbjuds av Data Factory:

- Azure Integration Runtime
- Integration Runtime med egen värd
- Azure SSIS Integration Runtime

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

Kör följande PowerShell-kommando för att hämta status för en instans av integration runtime (IR): 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdlet returnerar information om olika för olika typer av integration runtime. Den här artikeln beskriver egenskaper och status för varje typ av integrering runtime.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
Beräkningsresurser för en Azure-integrering körning hanteras helt Elastiskt i Azure. I följande tabell beskrivs egenskaperna som returneras av den **Get-AzureRmDataFactoryV2IntegrationRuntime** kommando:

### <a name="properties"></a>Egenskaper
Följande tabell innehåller beskrivningar av egenskaperna som returneras av cmdleten för en Azure-integrering körning:

| Egenskap | Beskrivning |
-------- | ------------- | 
| Namn | Namnet på Azure-integrering körningsmiljön. |  
| Status | Status för Azure-integrering körningsmiljön. | 
| Plats | Platsen för Azure-integrering körningsmiljön. Mer information om platsen för en Azure-integrering körning finns [introduktion till integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Namnet på datafabriken som Azure integration runtime tillhör. | 
| ResourceGroupName | Namnet på resursgruppen som data factory tillhör.  |
| Beskrivning | Beskrivning av integration körningsmiljön.  |

### <a name="status"></a>Status
Följande tabell innehåller olika statusar av en Azure-integrering körning:

| Status | Kommentarer-scenarier | 
| ------ | ------------------ |
| Online | Azure-integrering runtime är online och redo att användas. | 
| Offline | Azure-integrering runtime är offline på grund av ett internt fel. |

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd
Det här avsnittet innehåller beskrivningar för egenskaper som returneras av cmdleten Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Egenskaperna som returneras och status innehåller information om själva värdbaserade övergripande integrering runtime och varje nod i körningsmiljön.  

### <a name="properties"></a>Egenskaper

Följande tabell innehåller beskrivningar av övervakning av egenskaper för **varje nod**:

| Egenskap | Beskrivning | 
| -------- | ----------- | 
| Namn | Namnet på själva värdbaserade integration runtime och noder som är kopplade till den. Noden är ett lokalt Windows-dator som har den själv värdbaserade integration som är installerad på den. |  
| Status | Status för övergripande själva värdbaserade integrering runtime och varje nod. Exempel: Online/Offline/begränsad/etc. Information om dessa statusar finns i nästa avsnitt. | 
| Version | Versionen av själva värdbaserade integration runtime och varje nod. Versionen av körningsmiljön själva värdbaserade integration bestäms baserat på version av merparten av noder i gruppen. Om det finns noder med olika versioner i själva värdbaserade integration runtime inställningarna, egenvärdbaserat endast noder med samma versionsnummer som den logiska integrering runtime funktionen korrekt. Andra är i begränsat läge och måste uppdateras manuellt (bara om automatisk uppdatering misslyckas). | 
| Tillgängligt minne | Tillgängligt minne på en egen värdbaserade integration runtime-nod. Det här värdet är en nära realtid ögonblicksbild. | 
| Processoranvändning | Processoranvändningen för en egen värdbaserade integration runtime-nod. Det här värdet är en nära realtid ögonblicksbild. |
| Nätverk (In/ut) | Nätverksanvändning för en egen värdbaserade integration runtime-nod. Det här värdet är en nära realtid ögonblicksbild. | 
| Samtidiga jobb (kör / begränsa) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nära realtid ögonblicksbild. Gränsen innebär högsta samtidiga jobb för varje nod. Det här värdet definieras baserat på storleken på datorn. Du kan höja gränsen att skala upp samtidiga jobbkörningen i avancerade scenarier, om nätverks-CPU/minne är outnyttjad, men aktiviteter uppnådd tidsgräns. Den här funktionen finns också med en nod själva värdbaserade integration runtime. |
| Roll | Det finns två typer av roller i ett flernodigt själva värdbaserade integration körning – dispatcher- och arbetsroller. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher nod som används för att dra uppgifter/jobb från molntjänster och skicka dem till olika arbetsnoder. Noden dispatcher är också en arbetsnoden. |

Vissa inställningar för egenskaperna kände när det finns två eller flera noder (skalbar scenariot) i själva värdbaserade integration körningsmiljön. 
  
### <a name="status-per-node"></a>Status (per nod)
Följande tabell innehåller olika statusar för en egen värdbaserade integration runtime nod:

| Status | Beskrivning |
| ------ | ------------------ | 
| Online | Noden är ansluten till Data Factory-tjänsten. |
| Offline | Noden är offline. |
| Uppgraderar | Noden håller på att uppdateras automatiskt. |
| Begränsad | På grund av ett anslutningsproblem. Kan bero på HTTP-porten 8050 problemet, service bus-anslutningsproblem eller en autentiseringsuppgift synkroniseringsproblem. |
| Inaktiv | Noden är i en konfiguration som skiljer sig från konfigurationen av andra majoritet noder. |

En nod kan vara inaktiv när den inte kan ansluta till andra noder.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (övergripande själva värdbaserade integrering runtime)
Följande tabell innehåller olika statusar av en automatisk värdbaserade integration körning. Denna status beror på status för alla noder som tillhör körningsmiljön. 

| Status | Beskrivning |
| ------ | ----------- | 
| Behöver registrering | Ingen nod är registrerad för den här själva värdbaserade integration runtime ännu. |
| Online | Alla noder är online. |
| Offline | Ingen nod är online. |
| Begränsad | Inte alla noder i den här själva värdbaserade integration runtime är i felfritt tillstånd. Denna status är en varning om att vissa noder kanske inte körs. Den här statusen kan bero på en autentiseringsuppgift synkroniseringsproblem på dispatcher/arbetsnoden. |

Använd den **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** för att hämta JSON-nyttolast som innehåller den detaljerade egenvärdbaserat integrering runtime egenskaper och deras ögonblicksbild värden under körningen av den cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Exempel på utdata (förutsätter att det finns två noder som är associerade med den här själva värdbaserade integration runtime):

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
Azure-SSIS-integrering runtime är en helt hanterad kluster med Azure virtuella datorer (eller noder) som avsett för att köra SSIS-paket. Alla andra aktiviteter i Azure Data Factory körs inte. När etableringen har slutförts kan du fråga dess egenskaper och övervaka dess övergripande/nodspecifik status.

### <a name="properties"></a>Egenskaper

| Property/Status | Beskrivning |
| --------------- | ----------- |
| CreateTime | UTC-tid då din Azure-SSIS-integrering runtime skapades. |
| Noder | Allokerat/tillgängliga noderna i din Azure-SSIS-integrering runtime med nodspecifik status (starta/tillgängliga/återvinning/är inte tillgänglig) och går att åtgärda felen. |
| OtherErrors | Noden specifika tillämplig fel på din Azure-SSIS-integrering runtime. |
| LastOperation | Resultatet av den senaste Starta/stoppa åtgärden på ditt Azure-SSIS-integrering runtime med tillämplig fel om det inte. |
| Status | Övergripande status (inledande/startar/Starta/stoppa/stoppad) för din Azure-SSIS-integrering runtime. |
| Plats | Platsen för din Azure-SSIS-integrering runtime. |
| NodeSize | Storleken på varje nod i din Azure-SSIS-integrering runtime. |
| NodeCount | Antalet noder i din Azure-SSIS-integrering runtime. |
| MaxParallelExecutionsPerNode | Antalet parallella körningar per nod i din Azure-SSIS-integrering körningsmiljön. |
| CatalogServerEndpoint | Slutpunkten för din befintliga Azure SQL-instans i databasen/hanterad server till värd SSISDB. |
| CatalogAdminUserName | Administratörsanvändarnamnet för din befintliga Azure SQL-instans i databasen/hanterad server. Data Factory-tjänsten använder denna information för att förbereda och hantera SSISDB å dina vägnar. |
| CatalogAdminPassword | Admin-lösenordet för din befintliga Azure SQL-instans i databasen/hanterad server. |
| CatalogPricingTier | Prisnivån för SSISDB hos den befintliga Azure SQL Database-servern.  Inte tillämpligt för Azure SQL hanteras instans är värd för SSISDB. |
| VNetId | Virtuella nätverk (VNet) resurs-ID för din Azure-SSIS-integrering runtime att ansluta till. |
| Undernät | Undernätsnamn för din Azure-SSIS-integrering runtime att ansluta till. |
| ID | Resurs-ID för din Azure-SSIS-integrering runtime. |
| Typ | Typen (hanterade/egen-Hosted) för din Azure-SSIS-integrering runtime. |
| ResourceGroupName | Namnet på ditt Azure-resursgrupp som din data factory och Azure-SSIS-integrering runtime har skapats. |
| DataFactoryName | Namnet på din Azure data factory. |
| Namn | Namnet på ditt Azure-SSIS-integrering runtime. |
| Beskrivning | Beskrivning av din Azure-SSIS-integrering runtime. |

  
### <a name="status-per-node"></a>Status (per nod)

| Status | Beskrivning |
| ------ | ----------- | 
| Startar | Den här noden förbereds. |
| Tillgänglig | Den här noden är redo att distribuera/kör SSIS-paket. |
| Återvinning | Den här noden är att reparera/omstart. |
| Ej tillgänglig | Den här noden är inte redo att distribuera/kör SSIS-paket och tillämplig fel/problem som du kan lösa. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (övergripande Azure SSIS-integrering runtime)

| Övergripande status | Beskrivning | 
| -------------- | ----------- | 
| Inledande | Noderna i din Azure-SSIS-integrering runtime har inte allokerats/förberedd. | 
| Startar | Noderna i din Azure-SSIS-integrering runtime som allokerats/förberedd och fakturering har startats. |
| Startad | Noderna i din Azure-SSIS-integrering runtime har allokerats/förberedd och är redo att distribuera/kör SSIS-paket. |
| Stoppas  | Noderna i din Azure-SSIS-integrering runtime släpps. |
| Stoppad | Noderna i din Azure-SSIS-integrering runtime har publicerats och fakturering har stoppats. |

Finns i följande artiklar om du vill veta mer om Azure-SSIS-integrering körning:

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med självstudien och innehåller instruktioner om att använda Azure SQL-hanterade instanser (privat förhandsgranskning) och ansluta IR till ett virtuellt nätverk. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk (VNet). Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för att övervaka pipelines på olika sätt: 

- [Snabbstart: skapa en datafabrik](quickstart-create-data-factory-dot-net.md).
- [Använda Azure-Monitor för att övervaka Data Factory pipelines](monitor-using-azure-monitor.md)