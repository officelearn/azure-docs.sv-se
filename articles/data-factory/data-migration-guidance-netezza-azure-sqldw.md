---
title: Använd Azure Data Factory för att migrera data från den lokala Netezza-servern till Azure | Microsoft Docs
description: Använd Azure Data Factory för att migrera data från den lokala Netezza-servern till Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259566"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Använd Azure Data Factory för att migrera data från den lokala Netezza-servern till Azure 

Azure Data Factory ger en genomförd, robust och kostnads effektiv mekanism för att migrera data i stor skala från den lokala Netezza-servern till Azure Storage eller Azure SQL Data Warehouse. Den här artikeln innehåller följande information för data tekniker och utvecklare:

> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtning
> * Nätverkssäkerhet
> * Hög nivå lösnings arkitektur 
> * Metod tips för implementering  

## <a name="performance"></a>Prestanda

Azure Data Factory erbjuder en server lös arkitektur som tillåter parallellitet på olika nivåer, vilket gör att utvecklare kan bygga pipeliner för att fullt ut utnyttja din nätverks bandbredd samt databas bandbredd för att maximera data flödet för data förflyttning miljö.

![prestanda](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser: när du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) för varje kopierings aktivitet på ett Server lös sätt. När du använder Integration Runtime med egen värd kan du skala upp datorn manuellt eller skala ut till flera datorer ([upp till 4 noder](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) och en enda kopierings aktivitet distribuerar sin partition över alla noder. 
- En enskild kopierings aktivitet läser från och skriver till data lagret med hjälp av flera trådar. 
- Azure Data Factory kontroll flöde kan starta flera kopierings aktiviteter parallellt, till exempel använda [för varje slinga](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Du kan få mer information från [prestanda guiden för kopierings aktivitet](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Elasticitet

I en enda kopierings aktivitet körs Azure Data Factory en inbyggd återförsöks metod så att den kan hantera en viss nivå av tillfälliga fel i data lager eller i det underliggande nätverket.

Azure Data Factory kopierings aktivitet erbjuder också två sätt att hantera inkompatibla rader när du kopierar data mellan käll-och mottagar data lager. Du kan antingen avbryta och avbryta kopierings aktiviteten när inkompatibla data påträffas eller fortsätta att kopiera rest-data genom att hoppa över inkompatibla data rader. Dessutom kan du logga de inkompatibla raderna i Azure Blob Storage eller Azure Data Lake Store för att lära dig orsaken till felet, korrigera data på data källan och försöka kopiera aktiviteten igen.

## <a name="network-security"></a>Nätverkssäkerhet 

Azure Data Factory överför som standard data från den lokala Netezza-servern till Azure Storage eller Azure SQL Data Warehouse med hjälp av krypterad anslutning över HTTPS-protokoll. Den tillhandahåller data kryptering vid överföring och förhindrar avlyssning och man-in-the-middle-attacker.

Alternativt, om du inte vill att data ska överföras över offentliga Internet, kan du uppnå högre säkerhet genom att överföra data via en privat peering-länk via Azure Express Route. Se lösnings arkitekturen nedan om hur detta kan uppnås.

## <a name="solution-architecture"></a>Lösningsarkitektur

Migrera data över offentliga Internet:

![lösning – arkitektur – offentligt – nätverk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- I den här arkitekturen överförs data säkert med https över offentligt Internet.
- Du måste installera Azure Data Factory integration runtime med egen värd på en Windows-dator bakom företags brand vägg för att uppnå den här arkitekturen. Se till att din Azure Data Factory egen värd för integration runtime på en Windows-dator kan få direkt åtkomst till din Netezza-Server. Du kan skala upp datorn manuellt eller skala ut till flera datorer för att helt använda nätverket och data lagrar bandbredd för att kopiera data.
- Migreringen av både inledande ögonblicks data och delta data kan uppnås med den här arkitekturen.

Migrera data över privat länk: 

![lösning – arkitektur – privat nätverk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- I den här arkitekturen görs datamigrering via en privat peering-länk via Azure Express Route, så att data aldrig passerar över offentliga Internet. 
- Du måste installera Azure Data Factory integration runtime med egen värd på en virtuell Windows-dator i det virtuella Azure-nätverket för att uppnå den här arkitekturen. Du kan skala upp dina virtuella datorer manuellt eller skala ut till flera virtuella datorer för att helt använda nätverket och data lagrar bandbredd för att kopiera data.
- Migreringen av både inledande ögonblicks data och delta data kan uppnås med den här arkitekturen.

## <a name="implementation-best-practices"></a>Metod tips för implementering 

### <a name="authentication-and-credential-management"></a>Autentisering och hantering av autentiseringsuppgifter 

- Du kan använda [ODBC-autentisering via anslutnings sträng](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)för att autentisera till Netezza. 
- Flera typer av autentisering stöds för att ansluta till Azure Blob Storage.  Du rekommenderas att använda [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) : byggt ovanpå en automatiskt hanterad Azure Data Factory identifiera i Azure AD, men du kan konfigurera pipelines utan att ange autentiseringsuppgifter i den länkade tjänst definitionen.  Alternativt kan du autentisera till Azure Blob Storage med [tjänstens huvud namn](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [signatur för delad åtkomst](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)eller [lagrings konto nyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Flera autentiseringstyper stöds också för att ansluta till Azure Data Lake Storage Gen2.  Användning av [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) är starkt rekommenderat, även om [tjänstens huvud namn](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) eller [lagrings konto nyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) också kan användas. 
- Flera autentiseringstyper stöds också för att ansluta till Azure SQL Data Warehouse. Användning av [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) är starkt rekommenderat, även om [tjänstens huvud namn](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) eller [SQL-autentisering](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) också kan användas.
- Om du inte använder hanterade identiteter för Azure-resurser rekommenderar vi att du [sparar autentiseringsuppgifterna i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) att göra det enklare att hantera och rotera nycklar centralt utan att ändra Azure Data Factory länkade tjänster.  Detta är också en av de [rekommenderade metoderna för CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Första migrering av ögonblicks bild data 

För små tabeller när volym storleken är mindre än 100 GB, eller om den kan migreras till Azure inom 2 timmar, kan du göra varje data inläsnings data per tabell. Du kan köra flera Azure Data Factory kopierings jobb för att läsa in olika tabeller samtidigt för bättre data flöde. 

Inom varje kopierings jobb kan du också komma åt en viss nivå av parallellitet genom att använda [parallelCopies-inställningen](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) med alternativet datapartition för att köra parallella frågor och kopiera data efter partitioner. Det finns två alternativ för datapartition som ska väljas med information nedan.
- Du uppmanas att starta från data sektorn eftersom det är mer effektivt.  Kontrol lera att antalet paralleller i parallelCopies-inställningen är lägre än det totala antalet data sektor partitioner i tabellen på Netezza-servern.  
- Om volym storleken för varje data sektor partition fortfarande är stor (till exempel större än 10 GB) uppmanas du att byta till partitionen med dynamiskt intervall, där du får mer flexibilitet att definiera antalet partitioner och volym storleken för varje partition. efter partitionstabell, övre gränser och nedre gränser.

För stora tabeller när volym storleken är större än 100 GB, eller om den inte kan migreras till Azure inom 2 timmar, rekommenderar vi att du partitionerar data efter anpassad fråga och sedan kopierar varje kopierings jobb en partition i taget. Du kan köra flera Azure Data Factory kopierings jobb samtidigt för bättre data flöde. Tänk på att för varje kopierings jobb mål att läsa in en partition efter anpassad fråga kan du fortfarande aktivera parallellitet via antingen data sektorn eller det dynamiska intervallet för att öka data flödet. 

Om något av kopierings jobben Miss lyckas på grund av ett tillfälligt problem med nätverket eller data lagret kan du köra om det misslyckade kopierings jobbet för att läsa in den aktuella partitionen igen från tabellen. Alla andra kopierings jobb som läser in andra partitioner påverkas inte.

När du läser in data i Azure SQL Data Warehouse, föreslås PolyBase att aktive ras inom kopierings jobbet med Azure Blob Storage som mellanlagring.

### <a name="delta-data-migration"></a>Migrering av delta data 

Sättet att identifiera nya eller uppdaterade rader från tabellen använder en tidsstämpelkolumn eller en stegvis ökning av en nyckel i schemat och lagrar sedan det senaste värdet som en övre vatten märke i en extern tabell som kan användas för att filtrera delta data för nästa tids data inläsning. 

Olika tabeller kan använda olika vatten märkes kolumner för att identifiera nya eller uppdaterade rader. Vi rekommenderar att du skapar en extern kontroll tabell där varje rad representerar en tabell på Netezza-servern med dess angivna storlek för vatten märkes kolumnen och värdet för hög vatten märket. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Konfiguration av egen värd för integrerings körning på Azure VM eller Machine

Med tanke på att du migrerar data från Netezza-servern till Azure, oavsett om Netezza-servern är Promise bakom din företags brand vägg eller i en VNET-miljö, måste du installera integration runtime med egen värd på Windows-dator eller virtuell dator, vilket är motorn som ska flyttas data.

- Den rekommenderade konfigurationen att börja med för varje dator eller virtuell dator är med 32 vCPU och 128 GB minne. Du kan fortsätta att övervaka processor-och minnes användning för IR-datorn under datamigreringen för att se om du behöver skala upp datorn ytterligare för att få bättre prestanda eller skala ned datorn för att spara pengar.
- Du kan också skala ut genom att associera upp till 4 noder med en enda egen IR. Ett enda kopierings jobb som körs mot en lokal IR-IR kommer automatiskt att utnyttja alla VM-noder för att kopiera data parallellt. För hög tillgänglighet rekommenderar vi att du börjar med 2 VM-noder för att undvika en enskild felpunkt under datamigreringen.

### <a name="rate-limiting"></a>Hastighets begränsning

Vi rekommenderar att du utför en prestanda-POC med en representativ exempel data uppsättning, så att du kan fastställa lämplig partitionsstorlek för varje kopierings aktivitet. Vi rekommenderar att du får varje partition att läsas in på Azure inom 2 timmar.  

Börja med en enda kopierings aktivitet med en enda egen IR-dator för att kopiera en tabell. Öka parallelCopies gradvis baserat på antalet data sektor partitioner i tabellen och se om hela tabellen kan läsas in på Azure inom 2 timmar enligt det data flöde som du ser i kopierings jobbet. 

Om det inte går att uppnå, och samtidigt kapaciteten för IR-noden för egen värd och data lagret inte används fullt ut, ökar antalet samtidiga kopierings aktiviteter gradvis tills du når gränser för nätverket eller bandbredds gränsen för data lager. 

Fortsätt att övervaka PROCESSORns/minnes användningen på den lokala IR-datorn och var redo att skala upp datorn eller skala ut till flera datorer när du ser att CPU/minne används fullt ut. 

Om du stöter på fel som rapporter ATS av Azure Data Factory kopierings aktivitet kan du antingen minska samtidigheten eller parallelCopies-inställningen i Azure Data Factory eller överväga att öka bandbredds-/IOPS-gränserna för nätverket och data lager. 


### <a name="estimating-price"></a>Uppskattar pris 

Tänk på följande pipeline som skapats för att migrera data från den lokala Netezza-servern till Azure SQL Data Warehouse:

![priser – pipeline](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Låt oss anta följande: 

- Total data volym är 50 TB. 
- Migrera data med den första lösnings arkitekturen (Netezza-servern är lokal bakom brand väggen)
- 50 TB delas upp i 500 partitioner och varje kopierings aktivitet flyttar en partition.
- Varje kopierings aktivitet konfigureras med en egen värd-IR mot 4 datorer och ger 20 Mbit/s data flöde. (Inom kopierings aktiviteten är parallelCopies inställt på 4, och varje tråd för att läsa in data från tabellen uppnår 5 Mbit/s genom strömning)
- Förväntad concurrency är 3 och sammanställt data flöde är 60 Mbit/s.
- Totalt tar det 243 timmar att slutföra migreringen.

Här är det uppskattade priset baserat på ovanstående antaganden: 

![priser – tabell](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Detta är ett exempel på en hypotetisk prissättning. Din faktiska prissättning beror på det faktiska data flödet i din miljö. Priset för Windows-datorn (med egen värd för integration Runtime installerat) ingår inte. 

### <a name="additional-references"></a>Ytterligare referenser 
- [Datamigrering från det lokala Relations informations lagret till Azure med hjälp av Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza-anslutning](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC-anslutning](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob Storage-anslutningsapp](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 koppling](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse koppling](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Prestanda justerings guide för kopierings aktivitet](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Skapa och konfigurera egen värd Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Egen värd för integration runtime-tillgänglighet och skalbarhet](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Säkerhets överväganden vid data förflyttning](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Lagra autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiera data stegvis från en tabell](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Kopiera data stegvis från flera tabeller](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Sidan Azure Data Factory prissättning](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)