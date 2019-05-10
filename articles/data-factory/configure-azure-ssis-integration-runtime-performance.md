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
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232519"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurera Azure-SSIS Integration Runtime för höga prestanda

Den här artikeln beskriver hur du konfigurerar en Azure-SSIS Integration Runtime (IR) för hög prestanda. Azure-SSIS IR kan du distribuera och kör SQL Server Integration Services (SSIS)-paket i Azure. Mer information om Azure-SSIS IR finns i [integreringskörningen](concepts-integration-runtime.md#azure-ssis-integration-runtime) artikeln. Information om att distribuera och köra SSIS-paket på Azure finns i [Lift and shift SQL Serverintegration Services-arbetsbelastningar till molnet](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Den här artikeln innehåller prestandaresultat och observationer från interna testning målserverkonfigureringen medlemmar i SSIS-Utvecklingsteamet. Resultaten kan variera. Göra egna tester innan du avslutar konfigurationsinställningarna, vilket kan påverkar både kostnad och prestanda.

## <a name="properties-to-configure"></a>Egenskaper för att konfigurera

Följande delen av ett konfigurationsskript visar de egenskaper som du kan konfigurera när du skapar en Azure-SSIS Integration Runtime. Fullständig PowerShell-skript och beskrivning finns i [distribuera SQL Server Integration Services-paket till Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
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
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

I den inofficiella interna testning av SSIS-teknikerna, D-serien som verkar vara lämpligast för körning av SSIS-paket än A-serien.

-   Prestanda/pris förhållandet i D-serien är högre än A-serien och prestanda/pris förhållandet i v3-serien är högre än v2-serien.
-   Dataflöde för D-serien är högre än A-serien till samma pris och dataflödet för v3-serien är högre än v2-serien till samma pris.
-   Noder för v2-serien för Azure-SSIS IR är inte lämpligt för anpassad installation, så Använd v3-serien noderna istället. Om du redan använder noder för v2-serien, växla för att använda noder för v3-serien så snart som möjligt.
-   E-serien är minnesoptimerade VM-storlekar som ger en högre minne att CPU-förhållande än andra datorer. Om ditt paket kräver mycket minne, kan du välja virtuella datorer för E-serien.

### <a name="configure-for-execution-speed"></a>Konfigurera för körning hastighet
Om du inte har många paket för att köra och du vill paket för att köra snabbt, Använd informationen i följande diagram för att välja en typ av virtuell dator som är lämpliga för ditt scenario.

Dessa data representerar en och samma paketetkörning på en enskild worker-nod. Paketet läses in 3 miljoner poster med förnamn och senaste kolumner från Azure Blob Storage, genererar en kolumn för fullständigt namn och skriver poster som har det fullständiga namnet som är längre än 20 tecken till Azure Blob Storage.

![SSIS Integration Runtime-paketet körning hastighet](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurera för hela dataflödet

Om du har massor av paket för att köra och du är mest intresserad det totala arbetsflödet, Använd informationen i följande diagram för att välja en typ av virtuell dator som är lämpliga för ditt scenario.

![SSIS-Integreringskörning högsta totala arbetsflödet](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** justerar skalbarhet för integreringskörningen. Dataflödet för integration runtime är proportionell mot den **AzureSSISNodeNumber**. Ange den **AzureSSISNodeNumber** övervaka genomflödet av integration runtime till ett litet värde först och sedan justera värdet för ditt scenario. Om du vill konfigurera om nodantal worker, se [hantera en Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

När du redan använder en kraftfull arbetsnod för att köra paket, vilket ökar **AzureSSISMaxParallelExecutionsPerNode** kan öka det totala arbetsflödet av integration runtime. 1 – 4 parallella körningar per nod stöds för Standard_D1_v2 noder. För alla andra typer av noder stöds 1 max(2 x number of cores, 8) parallella körningar per nod. Om du vill att **AzureSSISMaxParallelExecutionsPerNode** utöver det högsta värde som vi stöds, kan du öppna ett supportärende och vi kan öka maxvärdet för dig och efter som du behöver använda Azure Powershell för att uppdatera  **AzureSSISMaxParallelExecutionsPerNode**.
Du kan beräkna lämpligt värde baserat på kostnaden för ditt paket och följande konfigurationer för arbetsnoderna. Mer information finns i [allmänna virtuella datorstorlekar](../virtual-machines/windows/sizes-general.md).

| Storlek             | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 2 / 2 x 500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 4 / 4 x 500                         | 2/1 500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 8 / 8 x 500                         | 4/3 000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 16 / 16 x 500                       | 8/6 000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4 000 / 80 / 40                                             | 8 / 8 x 500                         | 4/1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8 000 / 160 / 80                                            | 16 / 16 x 500                       | 8/2 000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3 000 / 46 / 23                                             | 4 / 6 x 500                         | 2/1 000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6 000 / 93 / 46                                             | 8 / 12 x 500                        | 2/2 000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12 000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24 000 / 375 / 187                                          | 32 / 48 x 500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48 000 / 750 / 375                                          | 32 / 96 x 500                       | 8/16 000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3 000 / 46 / 23                                             | 4 / 6 x 500                         | 2/1 000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6 000 / 93 / 46                                             | 8 / 12 x 500                        | 2/2 000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12 000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24 000 / 375 / 187                                          | 32 / 48 x 500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48 000 / 750 / 375                                          | 32 / 96 x 500                       | 8/16 000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8 / 30000                                      |

Här följer riktlinjerna för att ange rätt värde för den **AzureSSISMaxParallelExecutionsPerNode** egenskapen: 

1. Ange den till ett litet värde först.
2. Öka det med en viss att kontrollera om det totala arbetsflödet har förbättrats.
3. Stoppa öka värdet när det totala arbetsflödet når det högsta värdet.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** är prisnivån för SSIS-katalogdatabasen (SSISDB) på en Azure SQL database. Den här inställningen påverkar det maximala antalet arbeten i IR-instans, hur snabbt i kö för körning av ett paket och hastighet för att läsa in körningsloggen.

-   Om du inte bryr dig om hastighet för körning av kön paket och inläsning körningsloggen, kan du välja den lägsta prisnivån för databasen. Azure SQL Database med priser för grundläggande stöder 8 arbetare i en integration runtime-instans.

-   Välj en kraftfullare databas än grundläggande om antal arbeten är mer än 8 eller antal kärnor är mer än 50. Annars blir flaskhals av integration runtime-instans för databasen och serverns prestanda påverkas negativt.

-   Välj en kraftfullare databas, till exempel s3 om loggningsnivån anges till utförlig. Enligt våra inofficiella interna tester, har s3-prisinivå stöd för körning av SSIS-paket med 2 noder, 128 parallella antal och utförlig loggningsnivå.

Du kan också justera databasprisnivå utifrån [database-transaktionsenhet](../sql-database/sql-database-what-is-a-dtu.md) (DTU) användning-information är tillgänglig på Azure portal.

## <a name="design-for-high-performance"></a>Design för hög prestanda
Designa ett SSIS-paket för att köra i Azure skiljer sig från utforma ett paket för lokal körning. I stället för att kombinera flera oberoende aktiviteter i samma paket kan dela in dem i flera paket för effektivare körning i Azure-SSIS IR. Skapa en körning av paket för varje paket så att de inte behöver vänta på varandra för att slutföra. Den här metoden dra nytta av skalbarheten i Azure-SSIS integration runtime och förbättrar hela dataflödet.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-SSIS Integration Runtime. Se [Azure SSIS-Integreringskörning](concepts-integration-runtime.md#azure-ssis-integration-runtime).
