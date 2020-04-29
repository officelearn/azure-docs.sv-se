---
title: Övervaka integration runtime i Azure Data Factory
description: Lär dig hur du övervakar olika typer av integration runtime i Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 6d2ea5c0b7354867086fc0cce43732f2d73c53ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398951"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Övervaka en integreringskörning i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration runtime** är den beräknings infrastruktur som används av Azure Data Factory för att tillhandahålla olika funktioner för data integrering i olika nätverks miljöer. Det finns tre typer av integrerings körningar som erbjuds av Data Factory:

- Azure Integration Runtime
- Integration Runtime med egen värd
- Azure SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kör följande PowerShell-kommando för att hämta statusen för en instans av integration Runtime (IR): 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cmdleten returnerar annan information för olika typer av integration Runtime. I den här artikeln beskrivs egenskaper och status för varje typ av integration Runtime.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
Beräknings resursen för en Azure integration runtime hanteras helt elastiskt i Azure. Följande tabell innehåller beskrivningar av egenskaper som returneras av kommandot **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Egenskaper
Följande tabell innehåller beskrivningar av egenskaper som returneras av cmdleten för en Azure integration Runtime:

| Egenskap | Beskrivning |
-------- | ------------- | 
| Name | Namnet på Azure integration Runtime. |  
| Status | Status för Azure integration Runtime. | 
| Plats | Platsen för Azure integration Runtime. Mer information om platsen för en Azure integration runtime finns i [Introduktion till integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Namnet på den data fabrik som Azure integration runtime tillhör. | 
| ResourceGroupName | Namnet på den resurs grupp som data fabriken tillhör.  |
| Beskrivning | Beskrivning av integrerings körningen.  |

### <a name="status"></a>Status
Följande tabell innehåller möjliga status värden för en Azure integration Runtime:

| Status | Kommentarer/scenarier | 
| ------ | ------------------ |
| Online | Azure integration runtime är online och redo att användas. | 
| Offline | Azure integration runtime är offline på grund av ett internt fel. |

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd
Det här avsnittet innehåller beskrivningar av egenskaper som returneras av Get-AzDataFactoryV2IntegrationRuntime-cmdleten. 

> [!NOTE] 
> De returnerade egenskaperna och statusen innehåller information om den övergripande integrerings körningen med egen värd och varje nod i körnings miljön.  

### <a name="properties"></a>Egenskaper

Följande tabell innehåller beskrivningar av övervaknings egenskaper för **varje nod**:

| Egenskap | Beskrivning | 
| -------- | ----------- | 
| Name | Namnet på den egna värdbaserade integrerings körningen och noder som är kopplade till den. Node är en lokal Windows-dator som har den lokala integrerings körningen installerad. |  
| Status | Status för den övergripande integrerings körningen med egen värd och varje nod. Exempel: online/offline/begränsat/osv. Information om dessa statusar finns i nästa avsnitt. | 
| Version | Versionen av integration runtime med egen värd och varje nod. Versionen av den lokala integration runtime-versionen bestäms baserat på den version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i installations programmet för lokal installation av integration runtime fungerar bara noderna med samma versions nummer som den logiska integrerings körningen för egen värd. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering Miss lyckas). | 
| Tillgängligt minne | Tillgängligt minne på en egen värd för integration runtime-noden. Det här värdet är en nära real tids ögonblicks bild. | 
| CPU-användning | CPU-användning för en egen värd för integration runtime-noden. Det här värdet är en nära real tids ögonblicks bild. |
| Nätverk (in/ut) | Nätverks användning av en egen värd för integration runtime-noden. Det här värdet är en nära real tids ögonblicks bild. | 
| Samtidiga jobb (som körs/begränsas) | **Körs**. Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nära real tids ögonblicks bild. <br/><br/>**Begränsa**. Gränsen visar maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på datorns storlek. Du kan öka gränsen för att skala upp samtidiga jobb körningar i avancerade scenarier, när aktiviteterna är tids gräns, även om processor, minne eller nätverk används. Den här funktionen är också tillgänglig med en lokal integration runtime med egen värd. |
| Roll | Det finns två typer av roller i en integration runtime med egen värd för flera noder – dispatcher och Worker. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-nod som används för att hämta aktiviteter/jobb från moln tjänster och skicka dem till olika arbetsnoder. Dispatcher-noden är också en arbetsnod. |

Vissa inställningar för egenskaperna är mer begripliga när det finns två eller fler noder i den egna värdbaserade integrerings körningen (det vill säga i ett scenario med skala ut).

#### <a name="concurrent-jobs-limit"></a>Gräns för samtidiga jobb

Standardvärdet för gränsen för samtidiga jobb anges baserat på datorns storlek. De faktorer som används för att beräkna detta värde beror på mängden RAM-minne och antalet processor kärnor för datorn. Så ju fler kärnor och mer minne, desto högre är standard gränsen för samtidiga jobb.

Du skalar ut genom att öka antalet noder. När du ökar antalet noder är gränsen för samtidiga jobb summan av gräns värdena för samtidiga jobb för alla tillgängliga noder.  Om du till exempel använder en nod för att köra maximalt tolv samtidiga jobb, kan du köra högst 48 samtidiga jobb genom att lägga till tre likartade noder (det vill säga 4 × 12). Vi rekommenderar att du bara ökar gränsen för samtidiga jobb när du ser låg resursanvändning med standardvärdena på varje nod.

Du kan åsidosätta det beräknade standardvärdet i Azure Portal. Välj författare > anslutningar > integrerings körningar > redigera > noder > ändra värdet för samtidiga jobb per nod. Du kan också använda PowerShell [Update-Azdatafactoryv2integrationruntimenode-](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) kommandot.
  
### <a name="status-per-node"></a>Status (per nod)
Följande tabell innehåller möjliga status värden för en egen värd för integration runtime-noden:

| Status | Beskrivning |
| ------ | ------------------ | 
| Online | Noden är ansluten till Data Factory tjänsten. |
| Offline | Noden är offline. |
| Fortsätter | Noden uppdateras automatiskt. |
| Begränsad | På grund av ett anslutnings problem. Kan bero på HTTP-port 8050 problem, problem med Service Bus-anslutning eller problem med synkronisering av autentiseringsuppgifter. |
| Inaktiv | Noden har en annan konfiguration än konfigurationen av andra majoritets noder. |

En nod kan vara inaktiv när den inte kan ansluta till andra noder.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (övergripande integrerings körning med egen värd)
Följande tabell innehåller möjliga status värden för en lokal integration Runtime. Den här statusen beror på status för alla noder som tillhör körnings miljön. 

| Status | Beskrivning |
| ------ | ----------- | 
| Behöver registrering | Ingen nod har registrerats för den här integrerings körningen med egen värd. |
| Online | Alla noder är online. |
| Offline | Ingen nod är online. |
| Begränsad | Alla noder i den här integration runtime med egen värd är i felfritt tillstånd. Denna status är en varning om att vissa noder kan vara nere. Den här statusen kan bero på ett problem med synkronisering av autentiseringsuppgifter på dispatcher/arbetsnoden. |

Använd cmdleten **Get-AzDataFactoryV2IntegrationRuntimeMetric** för att hämta JSON-nyttolasten som innehåller de detaljerade egenskaperna för den egna värdbaserade integrerings körningen och deras värden för ögonblicks bilder under körningen av cmdleten.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Exempel på utdata (förutsätter att det finns två noder kopplade till den här integrerings körningen med egen värd):

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
Azure-SSIS integration runtime är ett fullständigt hanterat kluster av virtuella Azure-datorer (eller noder) som är dedikerade för att köra dina SSIS-paket. Den kör inga andra aktiviteter av Azure Data Factory. När du har upprättat kan du fråga dess egenskaper och övervaka de övergripande/Node-/regionsspecifika statusarna.

### <a name="properties"></a>Egenskaper

| Egenskap/status | Beskrivning |
| --------------- | ----------- |
| CreateTime | UTC-tiden när din Azure-SSIS integration runtime skapades. |
| Noder | De allokerade/tillgängliga noderna i din Azure-SSIS integration runtime med platsspecifika status (start/tillgänglighet/åter användning/ej tillgänglig) och fel som kan åtgärdas. |
| OtherErrors | Icke-nods åtgärds bara fel i Azure-SSIS integration Runtime. |
| LastOperation | Resultatet av den senaste start-/stopp åtgärden på din Azure-SSIS integration runtime med åtgärds bara fel om det Miss lyckas. |
| Status | Övergripande status (första/starta/starta/stoppa/stoppas) för din Azure-SSIS integration Runtime. |
| Plats | Platsen för din Azure-SSIS integration Runtime. |
| NodeSize | Storleken på varje nod i din Azure-SSIS integration Runtime. |
| NodeCount | Antalet noder i din Azure-SSIS integration Runtime. |
| MaxParallelExecutionsPerNode | Antalet parallella körningar per nod i din Azure-SSIS integration Runtime. |
| CatalogServerEndpoint | Slut punkten för din befintliga Azure SQL Database/hanterade instans server som värd för SSISDB. |
| CatalogAdminUserName | Admin-användarnamnet för din befintliga Azure SQL Database/hanterade instans Server. Data Factory tjänsten använder den här informationen för att förbereda och hantera SSISDB för din räkning. |
| CatalogAdminPassword | Administratörs lösen ordet för den befintliga Azure SQL Database/hanterade instans servern. |
| CatalogPricingTier | Pris nivån för SSISDB som finns på din befintliga Azure SQL Database-Server.  Inte tillämpligt för Azure SQL Database Hanterad instans som är värd för SSISDB. |
| VNetId | Det virtuella nätverks resurs-ID: t för din Azure-SSIS integration runtime för anslutning. |
| Undernät | Under näts namnet för din Azure-SSIS integration runtime för anslutning. |
| ID | Resurs-ID för Azure-SSIS integration Runtime. |
| Typ | Typen (hanterad/egen värd) för din Azure-SSIS integration Runtime. |
| ResourceGroupName | Namnet på din Azure-resurs, där din Data Factory och Azure-SSIS integration runtime skapades. |
| DataFactoryName | Namnet på din Azure Data Factory. |
| Name | Namnet på din Azure-SSIS integration Runtime. |
| Beskrivning | Beskrivningen av din Azure-SSIS integration Runtime. |

  
### <a name="status-per-node"></a>Status (per nod)

| Status | Beskrivning |
| ------ | ----------- | 
| Startar | Den här noden förbereds. |
| Tillgängligt | Den här noden är redo att distribuera/köra SSIS-paket. |
| Pappers | Den här noden repareras/startas om. |
| Inte tillgänglig | Den här noden är inte klar för att distribuera/köra SSIS-paket och har åtgärds bara fel/problem som du kan lösa. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (övergripande Azure-SSIS integration Runtime)

| Övergripande status | Beskrivning | 
| -------------- | ----------- | 
| Inledande | Noderna i din Azure-SSIS integration Runtime har inte allokerats/för berett. | 
| Startar | Noderna i Azure-SSIS integration runtime tilldelas/förbereds och faktureringen har påbörjats. |
| Börjat | Noderna i din Azure-SSIS integration Runtime har allokerats/för berett och är redo att distribuera/köra SSIS-paket. |
| Stoppas  | Noderna i din Azure-SSIS integration runtime släpps. |
| Stoppad | Noderna i din Azure-SSIS integration Runtime har släppts och faktureringen har stoppats. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Övervaka Azure-SSIS integration runtime i Azure Portal

Följande skärm bilder visar hur du väljer den Azure-SSIS IR som ska övervakas och ger ett exempel på den information som visas.

![Välj den Azure-SSIS integration runtime som ska övervakas](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Visa information om Azure-SSIS integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Övervaka Azure-SSIS integration runtime med PowerShell

Använd ett skript som i följande exempel för att kontrol lera status för Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mer information om Azure-SSIS integration runtime

I följande artiklar finns mer information om Azure-SSIS integration Runtime:

- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller konceptuell information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln är utökad i självstudien och innehåller instruktioner om hur du använder Azure SQL Database hanterade instansen och ansluter till IR till ett virtuellt nätverk. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Det innehåller också anvisningar för hur du använder Azure Portal för att konfigurera det virtuella nätverket så att Azure-SSIS IR kan ansluta till det virtuella nätverket. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för att övervaka pipelines på olika sätt: 

- [Snabb start: skapa en data fabrik](quickstart-create-data-factory-dot-net.md).
- [Använd Azure Monitor för att övervaka Data Factory pipelines](monitor-using-azure-monitor.md)
