---
title: Konfigurera prestanda för Azure-SSIS Integration Runtime | Microsoft Docs
description: Lär dig hur du konfigurerar egenskaper för din Azure-SSIS Integration Runtime för höga prestanda
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2592c81947f48c10891fe920647612d5c30af64f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989100"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurera Azure-SSIS Integration Runtime för höga prestanda

Den här artikeln beskriver hur du konfigurerar en Azure-SSIS Integration Runtime (IR) för hög prestanda. Azure-SSIS IR kan du distribuera och kör SQL Server Integration Services (SSIS)-paket i Azure. Mer information om Azure-SSIS IR finns i [integreringskörningen](concepts-integration-runtime.md#azure-ssis-integration-runtime) artikeln. Information om att distribuera och köra SSIS-paket på Azure finns i [Lift and shift SQL Serverintegration Services-arbetsbelastningar till molnet](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Den här artikeln innehåller prestandaresultat och observationer från interna testning målserverkonfigureringen medlemmar i SSIS-Utvecklingsteamet. Resultaten kan variera. Göra egna tester innan du avslutar konfigurationsinställningarna, vilket kan påverkar både kostnad och prestanda.

## <a name="properties-to-configure"></a>Egenskaper för att konfigurera

Följande delen av ett konfigurationsskript visar de egenskaper som du kan konfigurera när du skapar en Azure-SSIS Integration Runtime. Fullständig PowerShell-skript och beskrivning finns i [distribuera SQL Server Integration Services-paket till Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** är platsen för noden för integration runtime-worker. Arbetsnoden upprätthåller en anslutning till SSIS-katalogdatabasen (SSISDB) på en Azure SQL database. Ange den **AzureSSISLocation** till samma plats som SQL Database-server som är värd för SSISDB, vilket gör att integreringskörningen ska fungera så effektivt som möjligt.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, inklusive Azure-SSIS IR, stöder följande alternativ:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

I den inofficiella interna testning av SSIS-teknikerna, D-serien som verkar vara lämpligast för körning av SSIS-paket än A-serien.

-   Prestanda/pris förhållandet mellan D-serien är högre än A-serien.
-   Dataflöde för D-serien är högre än A-serien till samma pris.

### <a name="configure-for-execution-speed"></a>Konfigurera för körning hastighet
Om du inte har många paket för att köra och du vill paket för att köra snabbt, Använd informationen i följande diagram för att välja en typ av virtuell dator som är lämpliga för ditt scenario.

Dessa data representerar en och samma paketetkörning på en enskild worker-nod. Paketet läses in 10 miljoner poster med förnamn och senaste kolumner från Azure Blob Storage, genererar en kolumn för fullständigt namn och skriver poster som har det fullständiga namnet som är längre än 20 tecken till Azure Blob Storage.

![SSIS Integration Runtime-paketet körning hastighet](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Konfigurera för hela dataflödet

Om du har massor av paket för att köra och du är mest intresserad det totala arbetsflödet, Använd informationen i följande diagram för att välja en typ av virtuell dator som är lämpliga för ditt scenario.

![SSIS-Integreringskörning högsta totala arbetsflödet](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** justerar skalbarhet för integreringskörningen. Dataflödet för integration runtime är proportionell mot den **AzureSSISNodeNumber**. Ange den **AzureSSISNodeNumber** övervaka genomflödet av integration runtime till ett litet värde först och sedan justera värdet för ditt scenario. Om du vill konfigurera om nodantal worker, se [hantera en Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

När du redan använder en kraftfull arbetsnod för att köra paket, vilket ökar **AzureSSISMaxParallelExecutionsPerNode** kan öka det totala arbetsflödet av integration runtime. 1 – 4 parallella körningar per nod stöds för Standard_D1_v2 noder. För alla andra typer av noder stöds 1-8 parallella körningar per nod.
Du kan beräkna lämpligt värde baserat på kostnaden för ditt paket och följande konfigurationer för arbetsnoderna. Mer information finns i [allmänna virtuella datorstorlekar](../virtual-machines/windows/sizes-general.md).

| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 2 / 2 x 500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 4 / 4 x 500                         | 2/1 500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 8 / 8 x 500                         | 4/3 000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 16 / 16 x 500                       | 8/6 000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4 000 / 80 / 40                                             | 8 / 8 x 500                         | 4/1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8 000 / 160 / 80                                            | 16 / 16 x 500                       | 8/2 000                                       |

Här följer riktlinjerna för att ange rätt värde för den **AzureSSISMaxParallelExecutionsPerNode** egenskapen: 

1. Ange den till ett litet värde först.
2. Öka det med en viss att kontrollera om det totala arbetsflödet har förbättrats.
3. Stoppa öka värdet när det totala arbetsflödet når det högsta värdet.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** är prisnivån för SSIS-katalogdatabasen (SSISDB) på en Azure SQL database. Den här inställningen påverkar det maximala antalet arbeten i IR-instans, hur snabbt i kö för körning av ett paket och hastighet för att läsa in körningsloggen.

-   Om du inte bryr dig om hastighet för körning av kön paket och inläsning körningsloggen, kan du välja den lägsta prisnivån för databasen. Azure SQL Database med priser för grundläggande stöder 8 arbetare i en integration runtime-instans.

-   Välj en kraftfullare databas än grundläggande om antal arbeten är mer än 8 eller antal kärnor är mer än 50. Annars blir flaskhals av integration runtime-instans för databasen och serverns prestanda påverkas negativt.

Du kan också justera databasprisnivå utifrån [database-transaktionsenhet](../sql-database/sql-database-what-is-a-dtu.md) (DTU) användning-information är tillgänglig på Azure portal.

## <a name="design-for-high-performance"></a>Design för hög prestanda
Designa ett SSIS-paket för att köra i Azure skiljer sig från utforma ett paket för lokal körning. I stället för att kombinera flera oberoende aktiviteter i samma paket kan dela in dem i flera paket för effektivare körning i Azure-SSIS IR. Skapa en körning av paket för varje paket så att de inte behöver vänta på varandra för att slutföra. Den här metoden dra nytta av skalbarheten i Azure-SSIS integration runtime och förbättrar hela dataflödet.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-SSIS Integration Runtime. Se [Azure SSIS-Integreringskörning](concepts-integration-runtime.md#azure-ssis-integration-runtime).
