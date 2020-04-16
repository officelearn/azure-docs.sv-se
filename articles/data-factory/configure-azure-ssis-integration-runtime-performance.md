---
title: Konfigurera prestanda för Azure-SSIS Integration Runtime
description: Lär dig hur du konfigurerar egenskaperna för Azure-SSIS Integration Runtime för hög prestanda
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: ca88e42438c7cb48b062aa67d82053afbb9244bf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418294"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurera Azure-SSIS-integrationskörningen för hög prestanda

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


I den här artikeln beskrivs hur du konfigurerar en Azure-SSIS Integration Runtime (IR) för hög prestanda. Med Azure-SSIS IR kan du distribuera och köra SSIS-paket (SQL Server Integration Services) i Azure. Mer information om Azure-SSIS IR finns i artikeln [Integration runtime.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Information om hur du distribuerar och kör SSIS-paket på Azure finns i [Arbetsbelastningar för Sql Server Integration Services för att lyfta och flytta SQL Server Integration Services till molnet](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Den här artikeln innehåller prestandaresultat och observationer från interna tester som görs av medlemmar i SSIS utvecklingsteam. Dina resultat kan variera. Gör dina egna tester innan du slutför konfigurationsinställningarna, vilket påverkar både kostnader och prestanda.

## <a name="properties-to-configure"></a>Egenskaper som ska konfigureras

Följande del av ett konfigurationsskript visar de egenskaper som du kan konfigurera när du skapar en Azure-SSIS-integrationskörning. För det fullständiga PowerShell-skriptet och -beskrivningen finns i [Distribuera SQL Server Integration Services-paket till Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

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
**AzureSSISLocation** är platsen för integrationskörningsarbetarenoden. Arbetsnoden upprätthåller en konstant anslutning till SSIS Catalog-databasen (SSISDB) på en Azure SQL-databas. Ange **AzureSSISLocation** till samma plats som SQL Database-servern som är värd för SSISDB, vilket gör att integreringen kan fungera så effektivt som möjligt.

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

I den inofficiella interna testningen av SSIS-ingenjörsteamet verkar D-serien vara mer lämplig för SSIS-paketkörning än A-serien.

-   Prestanda/prisförhållandet för D-serien är högre än A-serien och prestanda/prisförhållandet för v3-serien är högre än v2-serien.
-   Dataflödet för D-serien är högre än A-serien till samma pris och dataflödet för v3-serien är högre än v2-serien till samma pris.
-   V2-seriens noder i Azure-SSIS IR är inte lämpliga för anpassad installation, så använd v3-seriens noder i stället. Om du redan använder v2-seriens noder, vänligen växla för att använda v3-seriens noder så snart som möjligt.
-   E-serien är minnesoptimerade VM-storlekar som ger ett högre förhållande mellan minne och PROCESSOR än andra datorer. Om ditt paket kräver mycket minne kan du överväga att välja virtuell E-serie.

### <a name="configure-for-execution-speed"></a>Konfigurera för körningshastighet
Om du inte har många paket att köra och du vill att paket ska köras snabbt använder du informationen i följande diagram för att välja en typ av virtuell dator som passar ditt scenario.

Dessa data representerar en enda paketkörning på en enda arbetsnod. Paketet läser in 3 miljoner poster med förnamn och efternamnskolumner från Azure Blob Storage, genererar en fullständig namnkolumn och skriver de poster som har det fullständiga namnet längre än 20 tecken till Azure Blob Storage.

![SSIS Integration Runtime-paketkörningshastighet](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurera för övergripande dataflöde

Om du har många paket att köra och du bryr dig mest om det totala dataflödet använder du informationen i följande diagram för att välja en typ av virtuell dator som passar ditt scenario.

![SSIS Integration Runtime maximalt totalt dataflöde](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** justerar skalbarheten för integrationskörningen. Dataflödet för integrationskörningen är proportionellt mot **AzureSSISNodeNumber**. Ange **AzureSSISNodeNumber** till ett litet värde först, övervaka dataflödet för integrationskörningen och justera sedan värdet för ditt scenario. Information om konfiguration av antalet arbetsnoder finns i [Hantera en Azure-SSIS-integreringskörning](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

När du redan använder en kraftfull arbetsnod för att köra paket kan ökande **AzureSSISMaxParallelExecutionsPerNode** öka det totala dataflödet för integrationskörningen. För Standard_D1_v2 noder stöds 1-4 parallella körningar per nod. För alla andra typer av noder stöds 1-max(2 x antal kärnor, 8) parallella körningar per nod. Om du vill ha **AzureSSISMaxParallelExecutionsPerNode** utöver det maxvärde vi stödde kan du öppna en supportbiljett och vi kan öka maxvärdet för dig och efter det behöver du använda Azure Powershell för att uppdatera **AzureSSISMaxParallelExecutionsPerNode**.
Du kan uppskatta lämpligt värde baserat på kostnaden för ditt paket och följande konfigurationer för arbetarnoderna. Mer information finns i [Allmänna storlekar för virtuella datorer](../virtual-machines/windows/sizes-general.md).

| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 2 / 2 x 500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 4 / 4 x 500                         | 2/1 500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 8 / 8 x 500                         | 4/3 000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 16 / 16 x 500                       | 8/6 000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4 000 / 80 / 40                                             | 8 / 8 x 500                         | 4/1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8 000 / 160 / 80                                            | 16 / 16 x 500                       | 8/2 000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3 000 / 46 / 23                                             | 4 / 6x500                         | 2/1 000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6 000 / 93 / 46                                             | 8 / 12x500                        | 2/2 000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12 000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24 000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48 000 / 750 / 375                                          | 32 / 96x500                       | 8/16 000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3 000 / 46 / 23                                             | 4 / 6x500                         | 2/1 000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6 000 / 93 / 46                                             | 8 / 12x500                        | 2/2 000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12 000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24 000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48 000 / 750 / 375                                          | 32 / 96x500                       | 8/16 000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Här är riktlinjerna för att ange rätt värde för egenskapen **AzureSSISMaxParallelExecutionsPerNode:** 

1. Ställ in den till ett litet värde i början.
2. Öka den med en liten mängd för att kontrollera om det totala dataflödet förbättras.
3. Sluta öka värdet när det totala dataflödet når det maximala värdet.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** är prisnivån för SSIS Catalog-databasen (SSISDB) i en Azure SQL-databas. Den här inställningen påverkar det maximala antalet arbetare i IR-instansen, hastigheten för att köa en paketkörning och hastigheten för att läsa in körningsloggen.

-   Om du inte bryr dig om hastigheten för att köa paketkörning och för att läsa in körningsloggen kan du välja den lägsta databasprisnivån. Azure SQL Database med grundläggande prissättning stöder 8 arbetare i en integrationskörningsinstans.

-   Välj en mer kraftfull databas än Basic om antalet anställda är fler än 8, eller så är kärnantalet mer än 50. Annars blir databasen flaskhalsen i integrationskörningsinstansen och det övergripande resultatet påverkas negativt.

-   Välj en mer kraftfull databas, till exempel s3 om loggningsnivån är inställd på att göra om du vill att den ska vara utförlig. Enligt våra inofficiella interna tester kan s3-prisnivå stödja körning av SSIS-paket med 2 noder, 128 parallella antal och utförlig loggningsnivå.

Du kan också justera databasprisnivån baserat på användningsinformation för [databastransaktionsenhet](../sql-database/sql-database-what-is-a-dtu.md) (DTU) som är tillgänglig på Azure-portalen.

## <a name="design-for-high-performance"></a>Design för hög prestanda
Att utforma ett SSIS-paket för att köras på Azure skiljer sig från att utforma ett paket för lokal körning. I stället för att kombinera flera oberoende uppgifter i samma paket, separera dem i flera paket för effektivare körning i Azure-SSIS IR. Skapa en paketkörning för varje paket, så att de inte behöver vänta på att varandra ska slutföras. Den här metoden drar nytta av skalbarheten för Azure-SSIS-integreringskörningen och förbättrar det övergripande dataflödet.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-SSIS Integration Runtime. Se [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
