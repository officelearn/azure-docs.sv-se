---
title: Migrera data från Amazon S3 till Azure Storage
description: Använd Azure Data Factory för att migrera data från Amazon S3 till Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122652"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Använda Azure Data Factory för att migrera data från Amazon S3 till Azure Storage 

Azure Data Factory tillhandahåller en högpresterande, robust och kostnadseffektiv mekanism för att migrera data i stor skala från Amazon S3 till Azure Blob Storage eller Azure Data Lake Storage Gen2.  Den här artikeln innehåller följande information för datatekniker och utvecklare: 

> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtningsförmåga
> * Nätverkssäkerhet
> * Lösningsarkitektur på hög nivå 
> * Bästa praxis för genomförande  

## <a name="performance"></a>Prestanda

ADF erbjuder en serverlös arkitektur som tillåter parallellism på olika nivåer, vilket gör det möjligt för utvecklare att bygga pipelines för att fullt ut utnyttja din nätverksbandbredd samt lagring IOPS och bandbredd för att maximera datarördataflödet för din miljö. 

Kunder har framgångsrikt migrerat petabyte data som består av hundratals miljoner filer från Amazon S3 till Azure Blob Storage, med ett ihållande dataflöde på 2 GB och högre. 

![prestanda](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Bilden ovan illustrerar hur du kan uppnå bra datarörelsehastigheter genom olika nivåer av parallellism:
 
- En enda kopieringsaktivitet kan dra nytta av skalbara beräkningsresurser: när du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) för varje kopieringsaktivitet på ett serverlöst sätt. När du använder självvärderad integrationskörning kan du manuellt skala upp datorn eller skala ut till flera datorer[(upp till 4 noder)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)och en enda kopieringsaktivitet partitionerar filuppsättningen över alla noder. 
- En enda kopieringsaktivitet läser från och skriver till datalagret med hjälp av flera trådar. 
- ADF-kontrollflöde kan starta flera kopieringsaktiviteter parallellt, till exempel med hjälp av [För varje slinga](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Motståndskraft

Inom en enda kopieringsaktivitetskörning har ADF inbyggd mekanism för återförsök så att den kan hantera en viss nivå av tillfälliga fel i datalagren eller i det underliggande nätverket. 

När du gör binärkopiering från S3 till Blob och från S3 till ADLS Gen2 utför ADF automatiskt kontrollpunkter.  Om en kopieringsaktivitetskörning har misslyckats eller timeats ut återupptas kopian från den senaste felpunkten i stället för att börja från början. 

## <a name="network-security"></a>Nätverkssäkerhet 

Som standard överför ADF data från Amazon S3 till Azure Blob Storage eller Azure Data Lake Storage Gen2 med krypterad anslutning via HTTPS-protokoll.  HTTPS tillhandahåller datakryptering under överföring och förhindrar avlyssning och man-in-the-middle-attacker. 

Alternativt, om du inte vill att data ska överföras via offentligt Internet, kan du uppnå högre säkerhet genom att överföra data via en privat peering-länk mellan AWS Direct Connect och Azure Express Route.  Se lösningsarkitekturen nedan om hur detta kan uppnås. 

## <a name="solution-architecture"></a>Lösningsarkitektur

Migrera data via offentligt Internet:

![lösning-arkitektur-public-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- I den här arkitekturen överförs data säkert med HTTPS via offentligt Internet. 
- Både källan Amazon S3 samt målet Azure Blob Storage eller Azure Data Lake Storage Gen2 är konfigurerade för att tillåta trafik från alla nätverks-IP-adresser.  Se den andra arkitekturen nedan om hur du kan begränsa nätverksåtkomsten till ett visst IP-intervall. 
- Du kan enkelt skala upp mängden hästkrafter på serverlöst sätt för att fullt ut utnyttja din nätverks- och lagringsbandbredd så att du kan få bästa genomströmning för din miljö. 
- Både inledande migrering av ögonblicksbilder och deltadatamigrering kan uppnås med den här arkitekturen. 

Migrera data via privat länk: 

![lösning-arkitektur-privat-nätverk](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- I den här arkitekturen görs datamigrering via en privat peering-länk mellan AWS Direct Connect och Azure Express Route så att data aldrig passerar över offentligt Internet.  Det kräver användning av AWS VPC och Azure Virtual Network. 
- Du måste installera ADF självvärderade integrationskörning på en Virtuell Windows-dator i ditt virtuella Azure-nätverk för att uppnå den här arkitekturen.  Du kan manuellt skala upp dina självvärderade IR-virtuella datorer eller skala ut till flera virtuella datorer (upp till 4 noder) för att fullt ut utnyttja din IOPS/bandbredd för nätverk och lagring. 
- Om det är acceptabelt att överföra data via HTTPS men du vill låsa nätverksåtkomsten till käll-S3 till ett visst IP-intervall kan du anta en variant av den här arkitekturen genom att ta bort AWS VPC och ersätta privat länk med HTTPS.  Du vill behålla Azure Virtual och självvärd ir på Azure VM så att du kan ha en statisk offentligt dirigerbar IP för vitlistning. 
- Både inledande migrering av ögonblicksbilddata och deltadatamigrering kan uppnås med den här arkitekturen. 

## <a name="implementation-best-practices"></a>Bästa praxis för genomförande 

### <a name="authentication-and-credential-management"></a>Autentisering och hantering av autentiseringsuppgifter 

- Om du vill autentisera till Amazon S3-konto måste du använda [åtkomstnyckeln för IAM-konto](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Flera autentiseringstyper stöds för att ansluta till Azure Blob Storage.  Användning av [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) rekommenderas starkt: bygger på en automatiskt hanterad ADF-identifiera i Azure AD, det gör att du kan konfigurera pipelines utan att ange autentiseringsuppgifter i Definitionen av länkad tjänst.  Du kan också autentisera till Azure Blob Storage med [Tjänsthuvudnamn,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)signatur för [delad åtkomst](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)eller [lagringskontonyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Flera autentiseringstyper stöds också för att ansluta till Azure Data Lake Storage Gen2.  Användning av [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) rekommenderas starkt, även om [tjänstens huvudnamn](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) eller [lagringskontonyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) också kan användas. 
- När du inte använder hanterade identiteter för Azure-resurser rekommenderas [lagring av autentiseringsuppgifterna i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) för att göra det enklare att centralt hantera och rotera nycklar utan att ändra ADF-länkade tjänster.  Detta är också en av de [bästa metoderna för CI / CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Inledande migrering av ögonblicksbilddata 

Datapartition rekommenderas särskilt när du migrerar mer än 100 TB data.  För att partitionera data, utnyttja "prefix" inställningen för att filtrera mappar och filer i Amazon S3 efter namn, och sedan varje ADF kopieringsjobb kan kopiera en partition i taget.  Du kan köra flera ADF-kopieringsjobb samtidigt för bättre dataflöde. 

Om något av kopieringsjobben misslyckas på grund av tillfälliga problem i nätverks- eller datalagringsplatsen kan du köra det misslyckade kopieringsjobbet igen för att läsa in den specifika partitionen igen från AWS S3.  Alla andra kopieringsjobb som läser in andra partitioner påverkas inte. 

### <a name="delta-data-migration"></a>Migrering av Delta-data 

Det mest högpresterande sättet att identifiera nya eller ändrade filer från AWS S3 är att använda tidspartitionerad namngivningskonvention – när dina data i AWS S3 har tid delats med tidssegmentinformation i filen eller mappnamnet (till exempel /yyyy/mm/dd/file.csv), då kan pipelinen enkelt identifiera vilka filer/mappar som ska kopieras stegvis. 

Alternativt, Om dina data i AWS S3 inte är tidspartitionerade, kan ADF identifiera nya eller ändrade filer med deras LastModifiedDate.   Hur det fungerar är att ADF kommer att skanna alla filer från AWS S3, och bara kopiera den nya och uppdaterade filen vars senaste modifierade tidsstämpel är större än ett visst värde.  Tänk på att om du har ett stort antal filer i S3 kan den första filskanningen ta lång tid oavsett hur många filer som matchar filtervillkoret.  I det här fallet föreslås du partitionera data först, med samma "prefix"-inställning för inledande ögonblicksbildmigrering, så att filskanningen kan ske parallellt.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>För scenarier som kräver självvärdbaserad integrationskörning på Azure VM 

Oavsett om du migrerar data via privat länk eller om du vill tillåta specifikt IP-intervall på Amazon S3-brandväggen måste du installera självvärdförd integrationskörningstid på Azure Windows VM. 

- Den rekommenderade konfigurationen som du kan starta med för varje Virtuell Azure-dator är Standard_D32s_v3 med 32 vCPU- och 128 GB-minne.  Du kan fortsätta att övervaka CPU- och minnesanvändningen för IR-datorn under datamigreringen för att se om du behöver skala upp den virtuella datorn ytterligare för bättre prestanda eller skala ned den virtuella datorn för att spara kostnader. 
- Du kan också skala ut genom att associera upp till 4 VM-noder med en enda självvärd iR.  Ett enda kopieringsjobb som körs mot en självvärd iR partitionerar automatiskt filuppsättningen och utnyttjar alla VM-noder för att kopiera filerna parallellt.  För hög tillgänglighet rekommenderas att du börjar med 2 VM-noder för att undvika enstaka felpunkt under datamigreringen. 

### <a name="rate-limiting"></a>Frekvensbegränsning 

Gör en POC-prestanda med en representativ exempeldatauppsättning så att du kan bestämma en lämplig partitionsstorlek. 

Börja med en enda partition och en enda kopieringsaktivitet med standardinställningen för DIU.  Öka gradvis DIU-inställningen tills du når bandbreddsgränsen för ditt nätverk eller IOPS/bandbreddsgränsen för datalagret, eller så har du nått max 256 DIU som tillåts på en enda kopieringsaktivitet. 

Öka sedan gradvis antalet samtidiga kopieringsaktiviteter tills du når gränserna för din miljö. 

När du stöter på begränsningsfel som rapporteras av ADF-kopieringsaktivitet, antingen minska samtidighet eller DIU-inställningen i ADF, eller överväga att öka bandbredden/IOPS-gränserna för nätverks- och datalager.  

### <a name="estimating-price"></a>Uppskatta pris 

> [!NOTE]
> Detta är ett hypotetiskt prisexempel.  Din faktiska prissättning beror på det faktiska dataflödet i din miljö.

Tänk på följande pipeline som skapats för att migrera data från S3 till Azure Blob Storage: 

![prissättning-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Låt oss anta följande: 

- Total datavolym är 2 PB 
- Migrera data via HTTPS med hjälp av den första lösningsarkitekturen 
- 2 PB är uppdelad i 1 K partitioner och varje kopia flyttar en partition 
- Varje kopia är konfigurerad med DIU=256 och uppnår 1 GBps-dataflöde 
- ForEach samtidighet är inställd på 2 och aggregerad genomströmning är 2 GBps 
- Totalt tar det 292 timmar att slutföra 

Här är det uppskattade priset baserat på ovanstående antaganden: 

![pris-tabell](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Ytterligare referenser 
- [Amazon Enkel lagringstjänst anslutning](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob Storage-anslutning](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-anslutning](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopiera inställningsguide för aktivitetsprestanda](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Skapa och konfigurera självvärderade integrationskörning](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Ha för integrering med självvärd för integration och skalbarhet](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Säkerhetsöverväganden vid dataflytt](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Spara autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiera filen stegvis baserat på tidspartiat filnamn](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiera nya och ändrade filer baserat på LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Prissida för ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Mall

Här är [mallen](solution-template-migration-s3-azure.md) till att börja med för att migrera petabyte data som består av hundratals miljoner filer från Amazon S3 till Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
