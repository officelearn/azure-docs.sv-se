---
title: Migrera Azure Data Lake Storage från Gen1 till Gen2
description: Migrera Azure Data Lake Storage från Gen1 till Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677133"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrera Azure Data Lake Storage från Gen1 till Gen2

Du kan migrera data, arbetsbelastningar och program från Data Lake Storage Gen1 till Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 bygger på [Azure Blob-lagring](storage-blobs-introduction.md) och innehåller en uppsättning funktioner som är dedikerade till stordataanalys. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) kombinerar funktioner från [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), till exempel filsystemssenmantik, katalog- och filnivåsäkerhet och skala med låg kostnad, nivåindelad lagring, hög tillgänglighet/katastrofåterställningsfunktioner från Azure [Blob-lagring](storage-blobs-introduction.md).

> [!NOTE]
> För enklare läsning använder den här artikeln termen *Gen1* för att referera till Azure Data Lake Storage Gen1 och termen *Gen2* för att referera till Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Rekommenderad metod

Om du vill migrera till Gen2 rekommenderar vi följande metod.

:heavy_check_mark: Steg 1: Bedöma beredskapen

:heavy_check_mark: Steg 2: Förbered dig på att migrera

:heavy_check_mark: Steg 3: Migrera data och programarbetsbelastningar

:heavy_check_mark: Steg 4: Cutover från Gen1 till Gen2

> [!NOTE]
> Gen1 och Gen2 är olika tjänster, det finns ingen på plats uppgradering erfarenhet, avsiktligmigrering ansträngning krävs. 

### <a name="step-1-assess-readiness"></a>Steg 1: Bedöma beredskapen

1. Lär dig mer om [Data Lake Storage Gen2-erbjudandet;](https://azure.microsoft.com/services/storage/data-lake-storage/) det är fördelar, kostnader och allmän arkitektur. 

2. [Jämför gen1:s funktioner](#gen1-gen2-feature-comparison) med Gen2:s. 

3. Granska en lista över [kända problem](data-lake-storage-known-issues.md) för att bedöma eventuella luckor i funktionaliteten.

4. Gen2 stöder Blob-lagringsfunktioner som [diagnostikloggning,](../common/storage-analytics-logging.md) [åtkomstnivåer](storage-blob-storage-tiers.md)och principer för [livscykelhantering för Blob-lagring](storage-lifecycle-management-concepts.md). Om du är intressant att använda någon av dessa funktioner, granska [den aktuella stödnivån](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features).

5. Granska det aktuella tillståndet för [Azure-ekosystemsupport](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) för att säkerställa att Gen2 stöder alla tjänster som dina lösningar är beroende av.

### <a name="step-2-prepare-to-migrate"></a>Steg 2: Förbered dig på att migrera

1. Identifiera de datauppsättningar som du ska migrera.

   Ta tillfället i akt att rensa datauppsättningar som du inte längre använder. Om du inte planerar att migrera alla data samtidigt, Ta dig tid att identifiera logiska grupper av data som du kan migrera i faser.
   
2. Ta reda på vilken inverkan en migrering kommer att ha på ditt företag.

   Tänk till exempel på om du har råd med några driftstopp medan migreringen sker. Dessa överväganden kan hjälpa dig att identifiera ett lämpligt migreringsmönster och välja de lämpligaste verktygen.

3. Skapa en migreringsplan. 

   Vi rekommenderar dessa [migreringsmönster](#migration-patterns). Du kan välja ett av dessa mönster, kombinera dem tillsammans eller utforma ett eget eget mönster.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Steg 3: Migrera data, arbetsbelastningar och program

Migrera data, arbetsbelastningar och program med hjälp av det mönster som du föredrar. Vi rekommenderar att du validerar scenarier stegvis.

1. [Skapa ett lagringskonto](data-lake-storage-quickstart-create-account.md) och aktivera den hierarkiska namnområdesfunktionen. 

2. Migrera dina data. 

3. Konfigurera [tjänster i dina arbetsbelastningar](data-lake-storage-integrate-with-azure-services.md) så att de pekar på din Gen2-slutpunkt. 
   
4. Uppdatera program för att använda Gen2 API:er. Se guider för [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) och [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Uppdatera skript för att använda Data Lake Storage Gen2 [PowerShell-cmdlets](data-lake-storage-directory-file-acl-powershell.md)och [Azure CLI-kommandon](data-lake-storage-directory-file-acl-cli.md).
   
6. Sök efter URI-referenser som `adl://` innehåller strängen i kodfiler, eller i Databricks-anteckningsböcker, Apache Hive HQL-filer eller någon annan fil som används som en del av dina arbetsbelastningar. Ersätt dessa referenser med [gen2-formaterad URI för](data-lake-storage-introduction-abfs-uri.md) ditt nya lagringskonto. Till exempel: Gen1 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` URI: `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`kan bli . 

7. Konfigurera säkerheten för ditt konto så att den innehåller [RBAC-roller (Role-based Access Control),](../common/storage-auth-aad-rbac-portal.md) [säkerhet på fil- och mappnivå](data-lake-storage-access-control.md)och [Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Steg 4: Cutover från Gen1 till Gen2

När du är säker på att dina program och arbetsbelastningar är stabila på Gen2 kan du börja använda Gen2 för att uppfylla dina affärsscenarier. Stäng av alla återstående pipelines som körs på Gen1 och inaktivera ditt Gen1-konto. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2-funktioner

I den här tabellen jämförs gen1:s funktioner med Gen2.

|Område |Gen1 (På andra)   |Gen2 (På andra) |
|---|---|---|
|Dataorganisation|[Hierarkiskt namnområde](data-lake-storage-namespace.md)<br>Stöd för filer och mappar|[Hierarkiskt namnområde](data-lake-storage-namespace.md)<br>Stöd för behållare, fil och mapp |
|Geo-redundans| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Autentisering|[AAD hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Huvudmän för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Huvudmän för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Nyckel för delad åtkomst](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Auktorisering|Förvaltning - [RBAC](../../role-based-access-control/overview.md)<br>Data – [ACL:er](data-lake-storage-access-control.md)|Förvaltning – [RBAC](../../role-based-access-control/overview.md)<br>Data - [ACL ,](data-lake-storage-access-control.md) [RBAC](../../role-based-access-control/overview.md) |
|Kryptering – Data i vila|Serversidan – med [Microsoft-hanterade](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) eller [kundhanterade](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nycklar|Serversidan – med [Microsoft-hanterade](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) eller [kundhanterade](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nycklar|
|VNET-stöd|[VNET-integrering](../../data-lake-store/data-lake-store-network-security.md)|[Tjänstslutpunkter](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [privata slutpunkter](../common/storage-private-endpoints.md)|
|Utvecklarupplevelse|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Allmänt tillgänglig - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Offentlig förhandsversion - [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Diagnostikloggar|Klassiska loggar<br>[Azure Monitor integrerad](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klassiska loggar](../common/storage-analytics-logging.md) - Allmänt tillgängliga<br>Azure-övervakarintegrering – tbd-skärmtid på tidslinjen|
|Ekosystem|[HDInsight (3.6),](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) [Azure Databricks (3.1 och högre),](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html) [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0),](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) [Azure Databricks (5.1 och högre),](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2) [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 till Gen2 mönster

Välj ett migreringsmönster och ändra sedan det mönstret efter behov.

|||
|---|---|
|**Lyft och skift**|Det enklaste mönstret. Perfekt om dina datapipelpipelsar har råd med driftstopp.|
|**Inkrementell kopia**|Liknar *lyft och skift*, men med mindre driftstopp. Perfekt för stora mängder data som tar längre tid att kopiera.|
|**Dubbel rörledning**|Perfekt för rörledningar som inte har råd med några driftstopp.|
|**Dubbelriktad synkronisering**|Liknar *dubbla rörledningar*, men med en mer stegvis strategi som lämpar sig för mer komplicerade rörledningar.|

Låt oss ta en närmare titt på varje mönster.
 
### <a name="lift-and-shift-pattern"></a>Lyft- och skiftmönster

Detta är det enklaste mönstret.

1. Stoppa alla skriver till Gen1.

2. Flytta data från Gen1 till Gen2. Vi rekommenderar [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACL:er kopieras med data.

3. Rikta in de åtgärder och arbetsbelastningar till Gen2.

4. Avveckling Gen1.

> [!div class="mx-imgBorder"]
> ![lyft- och skiftmönster](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Överväganden för användning av lyft- och skiftmönstret

:heavy_check_mark: Cutover från Gen1 till Gen2 för alla arbetsbelastningar samtidigt.

:heavy_check_mark: Räkna med driftstopp under migreringen och cutover-perioden.

: heavy_check_mark: Perfekt för pipelines som har råd med driftstopp och alla appar kan uppgraderas på en gång.

### <a name="incremental-copy-pattern"></a>Inkrementellt kopieringsmönster

1. Börja flytta data från Gen1 till Gen2. Vi rekommenderar [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACL:er kopieras med data.

2. Kopiera stegvis nya data från Gen1.

3. När alla data har kopierats stoppar du alla skrivningar till Gen1 och pekar arbetsbelastningar till Gen2.

4. Avveckling Gen1.

> [!div class="mx-imgBorder"]
> ![Inkrementellt kopieringsmönster](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Överväganden för användning av det inkrementella kopieringsmönstret:

:heavy_check_mark: Cutover från Gen1 till Gen2 för alla arbetsbelastningar samtidigt.

:heavy_check_mark: Räkna med driftstopp endast under cutover-perioden.

: heavy_check_mark: Perfekt för pipelines där alla appar uppgraderas på en gång, men datakopian kräver mer tid.

### <a name="dual-pipeline-pattern"></a>Mönster med dubbla rörledningar

1. Flytta data från Gen1 till Gen2. Vi rekommenderar [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACL:er kopieras med data.

2. Inta nya data till både Gen1 och Gen2.

3. Rikta arbetsbelastningar till Gen2.

4. Stoppa alla skrivningar till Gen1 och sedan avveckla Gen1.

> [!div class="mx-imgBorder"]
> ![Mönster med dubbla rörledningar](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Överväganden för användning av mönstret med dubbla rörledningar:

: heavy_check_mark: Gen1- och Gen2-rörledningar körs sida vid sida.

:heavy_check_mark: Stöder noll driftstopp.

: heavy_check_mark: Perfekt i situationer där dina arbetsbelastningar och program inte har råd med några driftstopp, och du kan inta i båda lagringskontona.

### <a name="bi-directional-sync-pattern"></a>Dubbelriktad synkroniseringsmönster

1. Ställ in dubbelriktad replikering mellan Gen1 och Gen2. Vi rekommenderar [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Det erbjuder en reparationsfunktion för befintliga data.

3. När alla drag är klara stoppar du alla skrivningar till Gen1 och inaktiverar dubbelriktad replikering.

4. Avveckling Gen1.

> [!div class="mx-imgBorder"]
> ![Dubbelriktat mönster](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Överväganden för användning av dubbelriktad synkroniseringsmönster:

:heavy_check_mark: Perfekt för komplexa scenarier som involverar ett stort antal pipelines och beroenden där en stegvis metod kan vara mer meningsfull.  

: heavy_check_mark: Migrationsinsatsen är hög, men den ger stöd sida vid sida för Gen1 och Gen2.

## <a name="next-steps"></a>Nästa steg

- Läs mer om de olika delarna av inrättandet av säkerhet för ett lagringskonto. Se [Azure Storage-säkerhetsguiden](../common/storage-security-guide.md).
- Optimera prestanda för ditt DataSjölager. Se [Optimera Azure Data Lake Storage Gen2 för prestanda](data-lake-storage-performance-tuning-guidance.md)
- Läs de bästa metoderna för att hantera din DataSjöbutik. Se [metodtips för användning av Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

