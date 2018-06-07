---
title: Konfigurera Azure SSIS-integrering Runtime för högpresterande | Microsoft Docs
description: Lär dig hur du konfigurerar egenskaperna för Azure-SSIS-integrering Runtime för hög prestanda
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 876f68d232e5f171f0c03b653d56f2351ffb2c7d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617135"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurera Azure SSIS-integrering Runtime för hög prestanda

Den här artikeln beskriver hur du konfigurerar en Azure-SSIS Integration Runtime (IR) för hög prestanda. Azure-SSIS-IR kan du distribuera och köra SQL Server Integration Services (SSIS) paket i Azure. Läs mer om Azure-SSIS IR [integrering runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) artikel. Information om distribution och körning av SSIS-paket i Azure finns [Lift och SKIFT SQL Serverintegration Services-arbetsbelastningar till molnet](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Den här artikeln innehåller prestandaresultat och observationer från interna testning av medlemmar i SSIS-Utvecklingsteamet. Resultatet kan variera. Göra egna tester innan du avslutar dina konfigurationsinställningar som påverkar både kostnad och prestanda.

## <a name="properties-to-configure"></a>Egenskaperna för att konfigurera

Följande delen av ett konfigurationsskript visar de egenskaper som du kan konfigurera när du skapar en Azure-SSIS-integrering körning. Fullständig PowerShell-skript och beskrivning finns [distribuera SQL Server Integration Services-paket till Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** är platsen för integrering runtime arbetsnoden. Arbetsnoden upprätthåller en anslutning till katalogen SSIS-databasen (SSISDB) på en Azure SQL database. Ange den **AzureSSISLocation** på samma plats som SQL Database-server som är värd för SSISDB som gör att körningen integrering ska fungera effektivt som möjligt.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Förhandsversion av Azure Data Factory v2, inklusive Azure SSIS-IR stöder följande alternativ:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

I den inofficiella interna tester av SSIS teknikteamet D-serien som verkar vara lämpligare för körning av SSIS-paket än A-serien.

-   Förhållandet mellan prestanda och pris i D-serien är högre än A-serien.
-   Dataflöde för D-serien är högre än A-series till samma pris.

### <a name="configure-for-execution-speed"></a>Konfigurera för körning av hastighet
Om du inte har många paket ska köras och du vill att paket ska köras snabbt, använda informationen i följande diagram för att välja en typ av virtuell dator som är lämpliga för ditt scenario.

Dessa data representerar en enda paket körning på en enskild worker-nod. Paketet läses in 10 miljoner poster med förnamn och senaste kolumner från Azure Blob Storage, genererar en fullständig namnkolumn och skriver de poster som har det fullständiga namnet som är längre än 20 tecken till Azure Blob Storage.

![SSIS-integrering Runtime paketet körning hastighet](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Konfigurera för totala genomflödet

Om du har många paket ska köras och du är mest intresserad av det totala genomflödet, använda informationen i följande diagram för att välja en typ av virtuell dator som är lämpliga för ditt scenario.

![SSIS-integrering Runtime maximala totala genomflödet](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** justerar skalbarhet av integration körningsmiljön. Dataflöde för integrering körningen är proportionell mot den **AzureSSISNodeNumber**. Ange den **AzureSSISNodeNumber** övervaka genomströmning av körningsmiljön integrering till ett mindre värde först och sedan justera värdet för ditt scenario. Om du vill konfigurera om antalet worker noden finns [hantera en Azure-SSIS-integrering körning](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

När du redan använder en kraftfull arbetsnod och köra paket, öka **AzureSSISMaxParallelExecutionsPerNode** kan öka det totala genomflödet av körningsmiljön integrering. Standard_D1_v2 noder stöder 1-4 parallella körningar per nod. För andra typer av noder stöds 1 – 8 parallella körningar per nod.
Du kan beräkna rätt värde baserat på paketet och följande konfigurationer för arbetarnoder kostnad. Mer information finns i [allmänna virtuella datorstorlekar](../virtual-machines/windows/sizes-general.md).

| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 2 / 2 x 500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 4 / 4 x 500                         | 2/1 500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 8 / 8 x 500                         | 4/3 000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 16 / 16 x 500                       | 8/6 000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4 000 / 80 / 40                                             | 8 / 8 x 500                         | 4/1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8 000 / 160 / 80                                            | 16 / 16 x 500                       | 8/2 000                                       |

Här följer riktlinjer för att ange rätt värde för den **AzureSSISMaxParallelExecutionsPerNode** egenskapen: 

1. Ange ett mindre värde först.
2. Öka det med ett litet för att kontrollera om det totala genomflödet förbättrats.
3. Stoppa öka värdet när det totala genomflödet når det högsta värdet.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** är prisnivå för katalogen SSIS-databasen (SSISDB) på en Azure SQL database. Den här inställningen påverkar det maximala antalet arbetare i IR-instans, hastighet till kön en paket-körning och hastighet för att läsa in körningsloggen.

-   Om du inte bryr dig om hastigheten till kön paketet körningen och att läsa in körningsloggen, kan du välja den lägsta databasprisnivå. Azure SQL Database med grundläggande priser stöder 8 arbetare i en integration runtime-instans.

-   Välj en kraftigare databas än grundläggande om antalet worker är mer än 8 eller antal kärnor är mer än 50. Annars databasen blir flaskhals för integrering runtime-instansen och prestanda påverkas negativt.

Du kan också justera databasprisnivå baserat på [database transaction unit](../sql-database/sql-database-what-is-a-dtu.md) (DTU) användningsinformation finns på Azure-portalen.

## <a name="design-for-high-performance"></a>Design för hög prestanda
Designa ett SSIS-paket ska köras på Azure skiljer sig från skapar ett paket för lokal körning. I stället för att kombinera flera oberoende aktiviteter i samma paket, dela in dem i flera paket för effektivare körning i Azure-SSIS-IR. Skapa ett paket-körning för varje paket så att de inte behöver vänta på att slutföras. Den här metoden fördelar från skalbarhet i Azure-SSIS-integrering runtime och förbättras det totala genomflödet.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-SSIS-integrering Runtime. Se [Azure SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
