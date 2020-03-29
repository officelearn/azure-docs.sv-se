---
title: Migrera data från en lokal Netezza-server till Azure
description: Använd Azure Data Factory för att migrera data från en lokal Netezza-server till Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: 80c9929f37b4890387a7625f04db6ce3e37f0cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74922123"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Använda Azure Data Factory för att migrera data från en lokal Netezza-server till Azure 

Azure Data Factory tillhandahåller en högpresterande, robust och kostnadseffektiv mekanism för att migrera data i stor skala från en lokal Netezza-server till ditt Azure-lagringskonto eller Azure SQL Data Warehouse-databas. 

Den här artikeln innehåller följande information för datatekniker och utvecklare:

> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtningsförmåga
> * Nätverkssäkerhet
> * Lösningsarkitektur på hög nivå 
> * Bästa praxis för genomförande  

## <a name="performance"></a>Prestanda

Azure Data Factory erbjuder en serverlös arkitektur som tillåter parallellism på olika nivåer. Om du är utvecklare innebär det att du kan skapa pipelines för att fullt ut använda både nätverks- och databasbandbredd för att maximera datarörflödet för din miljö.

![Diagram över prestanda](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Det föregående diagrammet kan tolkas på följande sätt:

- En enda kopieringsaktivitet kan dra nytta av skalbara beräkningsresurser. När du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) för varje kopieringsaktivitet på ett serverlöst sätt. Med en självvärderad integrationskörning (självvärd iR) kan du manuellt skala upp datorn eller skala ut till flera datorer[(upp till fyra noder)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)och en enda kopieringsaktivitet distribuerar partitionen över alla noder. 

- En enda kopieringsaktivitet läser från och skriver till datalagret med hjälp av flera trådar. 

- Azure Data Factory kontrollflöde kan starta flera kopieringsaktiviteter parallellt. Den kan till exempel starta dem med hjälp av en [För varje slinga](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Mer information finns i [Kopiera aktivitetsprestanda och skalbarhetsguide](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Motståndskraft

Inom en enda kopieringsaktivitetskörning har Azure Data Factory en inbyggd mekanism för återförsök, vilket gör det möjligt att hantera en viss nivå av tillfälliga fel i datalagren eller i det underliggande nätverket.

Med Azure Data Factory-kopieringsaktivitet har du två sätt att hantera inkompatibla rader när du kopierar data mellan käll- och sinkdatalager. Du kan antingen avbryta och misslyckas med kopieringsaktiviteten eller fortsätta att kopiera resten av data genom att hoppa över de inkompatibla dataraderna. Om du vill veta orsaken till felet kan du dessutom logga de inkompatibla raderna i Azure Blob storage eller Azure Data Lake Store, åtgärda data på datakällan och försöka kopiera aktiviteten igen.

## <a name="network-security"></a>Nätverkssäkerhet 

Som standard överför Azure Data Factory data från den lokala Netezza-servern till ett Azure-lagringskonto eller Azure SQL Data Warehouse-databas med hjälp av en krypterad anslutning via Https (Hypertext Transfer Protocol Secure). HTTPS tillhandahåller datakryptering under överföring och förhindrar avlyssning och man-in-the-middle-attacker.

Alternativt, om du inte vill att data ska överföras via det offentliga internet, kan du bidra till att uppnå högre säkerhet genom att överföra data via en privat peering-länk via Azure Express Route. 

I nästa avsnitt diskuteras hur du uppnår högre säkerhet.

## <a name="solution-architecture"></a>Lösningsarkitektur

I det här avsnittet beskrivs två sätt att migrera data.

### <a name="migrate-data-over-the-public-internet"></a>Migrera data över det offentliga internet

![Migrera data över det offentliga internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Det föregående diagrammet kan tolkas på följande sätt:

- I den här arkitekturen överför du data på ett säkert sätt med hjälp av HTTPS via det offentliga internet.

- För att uppnå den här arkitekturen måste du installera Azure Data Factory-integreringskörningen (självvärd) på en Windows-dator bakom en företagsbrandvägg. Kontrollera att den här integrationskörningen direkt kan komma åt Netezza-servern. Om du vill använda nätverket och data lagrar bandbredd för att kopiera data fullt ut kan du manuellt skala upp datorn eller skala ut till flera datorer.

- Med den här arkitekturen kan du migrera både inledande ögonblicksbildsdata och deltadata.

### <a name="migrate-data-over-a-private-network"></a>Migrera data över ett privat nätverk 

![Migrera data över ett privat nätverk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Det föregående diagrammet kan tolkas på följande sätt:

- I den här arkitekturen migrerar du data via en privat peering-länk via Azure Express Route och data passerar aldrig över det offentliga internet. 

- För att uppnå den här arkitekturen måste du installera Azure Data Factory-integreringskörningen (självvärd) på en virtuell Dator (VM) i ditt virtuella Azure-nätverk. Om du vill använda bandbredden för att kopiera data till fullo och data lagrar kan du manuellt skala upp den virtuella datorn eller skala ut till flera virtuella datorer.

- Med den här arkitekturen kan du migrera både inledande ögonblicksbildsdata och deltadata.

## <a name="implement-best-practices"></a>Implementera metodtips 

### <a name="manage-authentication-and-credentials"></a>Hantera autentisering och autentiseringsuppgifter 

- Om du vill autentisera till Netezza kan du använda [ODBC-autentisering via anslutningssträng](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 

- Så här autentiserar du till Azure Blob-lagring: 

   - Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Hanterad identitet som bygger på en automatiskt hanterad Azure Data Factory-identitet i Azure Active Directory (Azure AD) kan du konfigurera pipelines utan att behöva ange autentiseringsuppgifter i definitionen av länkad tjänst.  

   - Du kan också autentisera till Azure Blob-lagring med hjälp av [tjänsthuvudnamn,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)en [signatur för delad åtkomst](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)eller en [lagringskontonyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 

- Så här autentiserar du till Azure Data Lake Storage Gen2: 

   - Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity).
   
   - Du kan också använda [tjänstens huvudnamn](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) eller en [lagringskontonyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 

- Så här autentiserar du till Azure SQL Data Warehouse:

   - Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity).
   
   - Du kan också använda [tjänstens huvudnamn](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) eller [SQL-autentisering](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- När du inte använder hanterade identiteter för Azure-resurser rekommenderar vi starkt [att du lagrar autentiseringsuppgifterna i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) för att göra det enklare att centralt hantera och rotera nycklar utan att behöva ändra Azure Data Factory-länkade tjänster. Detta är också en av de [bästa metoderna för CI / CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrera inledande ögonblicksbilddata 

För små tabeller (det vill säga tabeller med en volym på mindre än 100 GB eller som kan migreras till Azure inom två timmar) kan du göra varje kopieringsjobbinläsningsdata per tabell. För större dataflöde kan du köra flera Azure Data Factory-kopieringsjobb för att läsa in separata tabeller samtidigt. 

Inom varje kopieringsjobb, för att köra parallella frågor och kopiera data efter partitioner, kan du också nå en viss nivå av parallellism genom att använda [ `parallelCopies` egenskapsinställningen](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) med något av följande datapartitionsalternativ:

- För att få hjälp med att uppnå större effektivitet rekommenderar vi att du börjar från en datasegment.  Kontrollera att värdet i `parallelCopies` inställningen är mindre än det totala antalet datasegmentpartitioner i tabellen på Netezza-servern.  

- Om volymen för varje datasegmentpartition fortfarande är stor (till exempel 10 GB eller mer) rekommenderar vi att du växlar till en partition med dynamiskt omfång. Det här alternativet ger dig större flexibilitet att definiera antalet partitioner och volymen för varje partition efter partitionskolumn, övre gräns och nedre gräns.

För större tabeller (det vill säga tabeller med en volym på 100 GB eller mer eller som *inte kan* migreras till Azure inom två timmar) rekommenderar vi att du partitionerar data efter anpassad fråga och sedan gör varje kopia-jobb kopiera en partition i taget. För bättre dataflöde kan du köra flera Azure Data Factory-kopieringsjobb samtidigt. För varje copy-job-mål för att läsa in en partition efter anpassad fråga kan du öka dataflödet genom att aktivera parallellism via antingen datasegment eller dynamiskt omfång. 

Om ett kopieringsjobb misslyckas på grund av ett tillfälligt problem i nätverks- eller datalagret kan du köra det misslyckade kopieringsjobbet igen för att läsa in den specifika partitionen från tabellen. Andra kopieringsjobb som läser in andra partitioner påverkas inte.

När du läser in data i en Azure SQL Data Warehouse-databas föreslår vi att du aktiverar PolyBase i kopieringsjobbet med Azure Blob-lagring som mellanlagring.

### <a name="migrate-delta-data"></a>Migrera deltadata 

Om du vill identifiera de nya eller uppdaterade raderna från tabellen använder du en tidsstämpelkolumn eller en stegvis nyckel i schemat. Du kan sedan lagra det senaste värdet som en hög vattenstämpel i en extern tabell och sedan använda det för att filtrera deltadata nästa gång du läser in data. 

Varje tabell kan använda en annan vattenstämpelkolumn för att identifiera de nya eller uppdaterade raderna. Vi föreslår att du skapar en extern kontrolltabell. I tabellen representerar varje rad en tabell på Netezza-servern med dess specifika vattenstämpelkolonnnamn och högt vattenstämpelvärde. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Konfigurera en självvärd integreringskörning

Om du migrerar data från Netezza-servern till Azure, oavsett om servern är lokal bakom företagets brandvägg eller i en virtuell nätverksmiljö, måste du installera en självvärd IR på en Windows-dator eller virtuell dator, som är den motor som används för att flytta data. När du installerar den självvärdbaserade IR rekommenderar vi följande metod:

- För varje Windows-dator eller virtuell dator börjar du med en konfiguration av 32 vCPU- och 128 GB-minne. Du kan fortsätta att övervaka CPU och minnesanvändning för IR-datorn under datamigreringen för att se om du behöver skala upp datorn ytterligare för bättre prestanda eller skala ned datorn för att spara kostnader.

- Du kan också skala ut genom att associera upp till fyra noder med en enda självvärd iR. Ett enda kopieringsjobb som körs mot en självvärd iR tillämpar automatiskt alla VM-noder för att kopiera data parallellt. För hög tillgänglighet, börja med fyra VM-noder för att undvika en enda felpunkt under datamigreringen.

### <a name="limit-your-partitions"></a>Begränsa dina partitioner

Gör ett prestandabevis (POC) med en representativ exempeldatauppsättning, så att du kan bestämma en lämplig partitionsstorlek för varje kopieringsaktivitet. Vi föreslår att du läser in varje partition till Azure inom två timmar.  

Om du vill kopiera en tabell börjar du med en enda kopieringsaktivitet med en enda, självvärd ir-dator. `parallelCopies` Öka inställningen gradvis baserat på antalet datasegmentpartitioner i tabellen. Se om hela tabellen kan läsas in i Azure inom två timmar, enligt dataflödet som är resultatet av kopieringsjobbet. 

Om den inte kan läsas in i Azure inom två timmar och kapaciteten för den självvärderade IR-noden och datalagret inte används fullt ut, ökar gradvis antalet samtidiga kopieringsaktiviteter tills du når gränsen för nätverket eller bandbreddsgränsen för data Butiker. 

Fortsätt att övervaka CPU- och minnesanvändningen på den självvärderade IR-datorn och var redo att skala upp datorn eller skala ut till flera datorer när du ser att processorn och minnet används fullt ut. 

När du stöter på begränsningsfel, som rapporterats av Azure Data Factory-kopieringsaktivitet, minskar du antingen samtidigheten eller `parallelCopies` inställningen i Azure Data Factory, eller överväg att öka bandbredds- eller I/O-åtgärder per sekund (IOPS) för nätverks- och datalager. 


### <a name="estimate-your-pricing"></a>Uppskatta din prissättning 

Tänk på följande pipeline, som är konstruerad för att migrera data från den lokala Netezza-servern till en Azure SQL Data Warehouse-databas:

![Prispipeline](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Låt oss anta att följande påståenden är sanna: 

- Den totala datavolymen är 50 terabyte (TB). 

- Vi migrerar data med hjälp av arkitektur med första lösning (Netezza-servern är lokal, bakom brandväggen).

- Volymen på 50 TB är uppdelad i 500 partitioner och varje kopieringsaktivitet flyttar en partition.

- Varje kopieringsaktivitet konfigureras med en självvärd IR mot fyra datorer och uppnår ett dataflöde på 20 megabyte per sekund (MBps). (Inom kopieringsaktivitet är `parallelCopies` inställd på 4 och varje tråd för att läsa in data från tabellen uppnår ett 5-MBps-dataflöde.)

- ForEach-samtidigheten är inställd på 3 och det sammanlagda genomströmflödet är 60 MBps.

- Totalt tar det 243 timmar att slutföra migreringen.

Baserat på föregående antaganden, här är det uppskattade priset: 

![Pristabellen](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Prissättningen som visas i föregående tabell är hypotetisk. Din faktiska prissättning beror på det faktiska dataflödet i din miljö. Priset för Windows-datorn (med den självvärdbaserade IR-enheten installerad) ingår inte. 

### <a name="additional-references"></a>Ytterligare referenser

Mer information finns i följande artiklar och guider:

- [Migrera data från en lokal relationsdatalagerdatabas till Azure med hjälp av Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza-kontakt](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC-anslutningsapp](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob-lagringsappen](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-anslutning](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse-anslutning](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Kopiera inställningsguide för aktivitetsprestanda](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Skapa och konfigurera lokalt installerad integrationskörning](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Ha för integrering med självvärd för integration och skalbarhet](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Säkerhetsöverväganden vid dataflytt](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Spara autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiera data stegvis från en tabell](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Kopiera data stegvis från flera tabeller](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Prissida för Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med hjälp av Azure Data Factory](solution-template-copy-files-multiple-containers.md)
