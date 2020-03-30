---
title: Övervaka integrationskörningen i Azure Data Factory
description: Lär dig hur du övervakar olika typer av integrationskörning i Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 2399849b87e44c5cb70d2db987ae18d8d2d9c552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261143"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Övervaka en integreringskörning i Azure Data Factory  
**Integrationskörning** är den beräkningsinfrastruktur som används av Azure Data Factory för att tillhandahålla olika dataintegrationsfunktioner i olika nätverksmiljöer. Det finns tre typer av integrationskörningar som erbjuds av Data Factory:

- Azure integration runtime
- Integration Runtime med egen värd
- Azure SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill hämta status för en instans av integrationskörning (IR) kör du följande PowerShell-kommando: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdlet returnerar olika information för olika typer av integrationskörning. I den här artikeln beskrivs egenskaperna och statusarna för varje typ av integrationskörning.  

## <a name="azure-integration-runtime"></a>Azure integration runtime
Beräkningsresursen för en Azure-integreringskörning hanteras helt elastiskt i Azure. I följande tabell finns beskrivningar av egenskaper som returneras av kommandot **Get-AzDataFactoryV2IntegrationRuntime:**

### <a name="properties"></a>Egenskaper
I följande tabell finns beskrivningar av egenskaper som returnerats av cmdlet för en Azure-integreringskörning:

| Egenskap | Beskrivning |
-------- | ------------- | 
| Namn | Namn på Azure-integreringskörningen. |  
| Status | Status för Azure-integreringskörningen. | 
| Location | Plats för Azure-integreringskörningen. Mer information om platsen för en Azure-integreringskörning finns i [Introduktion till integrationskörning](concepts-integration-runtime.md). |
| DataFactoryName | Namnet på den datafabrik som Azure-integreringen körs på. | 
| ResourceGroupName | Namn på den resursgrupp som datafabriken tillhör.  |
| Beskrivning | Beskrivning av integrationskörningen.  |

### <a name="status"></a>Status
Följande tabell innehåller möjliga status för en Azure-integreringskörning:

| Status | Kommentarer/scenarier | 
| ------ | ------------------ |
| Online | Azure-integreringskörningen är online och klar att användas. | 
| Offline | Azure-integreringskörningen är offline på grund av ett internt fel. |

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd
Det här avsnittet innehåller beskrivningar av egenskaper som returneras av cmdleten Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> De returnerade egenskaperna och statusen innehåller information om övergripande självvärdbaserad integrationskörning och varje nod i körningen.  

### <a name="properties"></a>Egenskaper

I följande tabell finns beskrivningar av övervakningsegenskaper för **varje nod:**

| Egenskap | Beskrivning | 
| -------- | ----------- | 
| Namn | Namnet på den självvärderade integrationskörningen och noder som är associerade med den. Nod är en lokal Windows-dator som har den självvärderade integrationskörningen installerad på den. |  
| Status | Status för den övergripande självvärderade integrationskörningen och varje nod. Exempel: Online/Offline/Limited/etc. Information om dessa statusar finns i nästa avsnitt. | 
| Version | Versionen av självvärdbaserad integrationskörning och varje nod. Versionen av den självvärderade integrationskörningen bestäms baserat på version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i den självvärderade integrationskörningskonfigurationen är det bara noderna med samma versionsnummer som den logiska självvärderade integrationskörningsfunktionen korrekt. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering misslyckas). | 
| Tillgängligt minne | Tillgängligt minne på en självvärdbaserad integrationskörningsnod. Det här värdet är en ögonblicksbild i nära realtid. | 
| CPU-användning | CPU-användning av en självvärderad integrationskörningsnod. Det här värdet är en ögonblicksbild i nära realtid. |
| Nätverk (in/ut) | Nätverksanvändning av en självvärdbaserad integrationskörningsnod. Det här värdet är en ögonblicksbild i nära realtid. | 
| Samtidiga jobb (löpning/gräns) | **Kör**. Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en ögonblicksbild i nära realtid. <br/><br/>**Gräns**. Limit betyder maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på maskinens storlek. Du kan öka gränsen för att skala upp samtidiga jobbkörning i avancerade scenarier, när aktiviteter tar tid att ta ut även när CPU, minne eller nätverk är underutnyttjade. Den här funktionen är också tillgänglig med en självvärd för integreringskörning med en nod. |
| Roll | Det finns två typer av roller i en självvärdbaserad integrationskörning för flera nod – avsändare och arbetare. Alla noder är arbetare, vilket innebär att de alla kan användas för att utföra jobb. Det finns bara en dispatcher-nod, som används för att hämta uppgifter/jobb från molntjänster och skicka dem till olika arbetsnoder. Dispatcher-noden är också en arbetarnod. |

Vissa inställningar för egenskaperna är mer meningsfulla när det finns två eller flera noder i den självvärderade integrationskörningen (det vill i ett utskalningsscenario).

#### <a name="concurrent-jobs-limit"></a>Gränsen för samtidiga jobb

Standardvärdet för gränsen för samtidiga jobb anges baserat på maskinens storlek. De faktorer som används för att beräkna detta värde beror på mängden RAM och antalet CPU-kärnor på maskinen. Så ju fler kärnor och ju mer minne, desto högre standardgräns för samtidiga jobb.

Du skalar ut genom att öka antalet noder. När du ökar antalet noder är gränsen för samtidiga jobb summan av de samtidiga jobbgränsvärdena för alla tillgängliga noder.  Om en nod till exempel kan köra högst tolv samtidiga jobb kan du köra högst 48 samtidiga jobb (det vill säga 4 x 12). Vi rekommenderar att du bara ökar gränsen för samtidiga jobb när du ser låg resursanvändning med standardvärdena på varje nod.

Du kan åsidosätta det beräknade standardvärdet i Azure-portalen. Välj Författare > Anslutningar > integrationskörningar > Redigera > noder > Ändra samtidiga jobbvärde per nod. Du kan också använda kommandot PowerShell [update-Azdatafactoryv2integrationruntimenode.](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples)
  
### <a name="status-per-node"></a>Status (per nod)
Följande tabell innehåller möjliga status för en självvärdbaserad integrationskörningsnod:

| Status | Beskrivning |
| ------ | ------------------ | 
| Online | Noden är ansluten till tjänsten Data Factory. |
| Offline | Noden är offline. |
| Uppgradering | Noden uppdateras automatiskt. |
| Begränsad | På grund av ett anslutningsproblem. Kan bero på problem med HTTP-port 8050, servicebussanslutningsproblem eller ett synkroniseringsproblem för autentiseringsuppgifter. |
| Inaktiv | Noden är i en annan konfiguration än konfigurationen av andra majoritetsnoder. |

En nod kan vara inaktiv när den inte kan ansluta till andra noder.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (övergripande självvärd för integrationskörning)
Följande tabell innehåller möjliga status för en självvärderad integrationskörning. Den här statusen beror på status för alla noder som tillhör körningen. 

| Status | Beskrivning |
| ------ | ----------- | 
| Behöver registrering | Ingen nod har registrerats för den här självvärderade integrationskörningen ännu. |
| Online | Alla noder är online. |
| Offline | Ingen nod är online. |
| Begränsad | Alla noder i den här självvärderade integrationskörningen är inte felfritt. Den här statusen är en varning om att vissa noder kan vara nere. Den här statusen kan bero på ett synkroniseringsproblem för autentiseringsuppgifter på avsändarens/arbetarnoden. |

Använd cmdleten **Get-AzDataFactoryV2IntegrationRuntimeMetric** för att hämta JSON-nyttolasten som innehåller de detaljerade självvärderade integrationskörningsegenskaperna och deras ögonblicksbildvärden under körningstiden för cmdleten.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Exempelutdata (förutsätter att det finns två noder som är associerade med den här självvärderade integrationskörningen):

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
Azure-SSIS-integreringskörning är ett fullständigt hanterat kluster av virtuella Azure-datorer (eller noder) som är dedikerade för att köra dina SSIS-paket. Det körs inte några andra aktiviteter i Azure Data Factory. När du har etablerat dem kan du fråga om dess egenskaper och övervaka dess övergripande/nodspecifika status.

### <a name="properties"></a>Egenskaper

| Egenskap/status | Beskrivning |
| --------------- | ----------- |
| SkapaTid | UTC-tiden när din Azure-SSIS-integreringskörning skapades. |
| Noder | De allokerade/tillgängliga noderna för din Azure-SSIS-integreringskörning med nodspecifika statusar (start/tillgänglig/återvinning/ej tillgänglig) och användbara fel. |
| OtherErrors | De icke-nodspecifika åtgärdsbara felen i din Azure-SSIS-integreringskörning. |
| Senasteoperation | Resultatet av senaste start/stopp-åtgärden på din Azure-SSIS-integreringskörning med åtgärdsbara fel om det misslyckades. |
| Status | Den övergripande statusen (start/start/started/stopping/stopped) för din Azure-SSIS-integreringskörning. |
| Location | Platsen för din Azure-SSIS-integreringskörning. |
| NodSize | Storleken på varje nod i din Azure-SSIS-integreringskörning. |
| Antal node | Antalet noder i din Azure-SSIS-integreringskörning. |
| MaxParallelExecutionsPerNode | Antalet parallella körningar per nod i din Azure-SSIS-integreringskörning. |
| KatalogServerEndpoint | Slutpunkten för din befintliga Azure SQL Database/Managed Instance-server som värd för SSISDB. |
| KatalogAdminUserName | Administratörsanvändarnamnet för din befintliga Azure SQL Database/Managed Instance-server. Data Factory-tjänsten använder denna information för att förbereda och hantera SSISDB för din räkning. |
| KatalogAdminPassword | Administratörslösenordet för din befintliga Azure SQL Database/Managed Instance-server. |
| CatalogPricingTier | Prisnivån för SSISDB som finns värd för din befintliga Azure SQL Database-server.  Inte tillämplig på Azure SQL Database Managed Instance som är värd för SSISDB. |
| VNetId (på andra) | Det virtuella nätverksresurs-ID:et för din Azure-SSIS-integreringskörning att gå med i. |
| Undernät | Undernätsnamnet för din Azure-SSIS-integreringskörning att gå med i. |
| ID | Resurs-ID för din Azure-SSIS-integreringskörning. |
| Typ | Typen (Hanterad/självvärd) för din Azure-SSIS-integreringskörning. |
| ResourceGroupName | Namnet på din Azure Resource Group, där din datafabrik och Azure-SSIS-integreringskörning har skapats. |
| DataFactoryName | Namnet på din Azure-datafabrik. |
| Namn | Namnet på din Azure-SSIS-integreringskörning. |
| Beskrivning | Beskrivningen av din Azure-SSIS-integreringskörning. |

  
### <a name="status-per-node"></a>Status (per nod)

| Status | Beskrivning |
| ------ | ----------- | 
| Startar | Noden förbereds. |
| Tillgängligt | Den här noden är klar för dig att distribuera/köra SSIS-paket. |
| Återvinning | Den här noden repareras/startas om. |
| Inte tillgänglig | Den här noden är inte redo för distribution/körning av SSIS-paket och har användbara fel/problem som du kan lösa. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (övergripande Azure-SSIS-integreringskörning)

| Övergripande status | Beskrivning | 
| -------------- | ----------- | 
| Inledande | Noderna för din Azure-SSIS-integreringskörning har inte allokerats/förberetts. | 
| Startar | Noderna för din Azure-SSIS-integreringskörning allokeras/förbereds och faktureringen har startats. |
| Börjat | Noderna för din Azure-SSIS-integreringskörning har allokerats/förberetts och de är redo för distribution/körning av SSIS-paket. |
| Stoppas  | Noderna för din Azure-SSIS-integreringskörning släpps. |
| Stoppad | Noderna för din Azure-SSIS-integreringskörning har släppts och faktureringen har stoppats. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Övervaka Azure-SSIS-integreringskörningen i Azure-portalen

Följande skärmbilder visar hur du väljer Azure-SSIS IR att övervaka och ger ett exempel på den information som visas.

![Välj azure-SSIS-integreringskörningen för att övervaka](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Visa information om Azure-SSIS-integreringskörningen](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Övervaka Azure-SSIS-integreringskörningen med PowerShell

Använd ett skript som följande exempel för att kontrollera status för Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mer information om Azure-SSIS-integreringskörningen

Se följande artiklar om du vill veta mer om Azure-SSIS-integreringskörning:

- [Azure-SSIS-integreringskörning](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller begreppsmässig information om integrationskörningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökar självstudien och innehåller instruktioner om hur du använder Azure SQL Database Managed Instance och ansluter till IR till ett virtuellt nätverk. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Det innehåller också steg för att använda Azure-portalen för att konfigurera det virtuella nätverket så att Azure-SSIS IR kan ansluta till det virtuella nätverket. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för övervakning av rörledningar på olika sätt: 

- [Snabbstart: skapa en datafabrik.](quickstart-create-data-factory-dot-net.md)
- [Använda Azure Monitor för att övervaka Data Factory-pipelines](monitor-using-azure-monitor.md)
