---
title: Integration runtime
description: Mer information om Integration Runtime i Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: ffa348c796a4d9d4e3bdb8e7ce18ba0eb82e17ad
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418396"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime i Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att ge olika nätverksmiljöer integrationsfunktioner:

- **Dataflöde**: Kör ett [dataflöde](concepts-data-flow-overview.md) i hanterad Azure-beräkningsmiljö.  
- **Dataförflyttning**: Kopiera data mellan datalager i offentliga nätverk och datalager i privat nätverk (lokalt eller virtuellt privat nätverk). Den ger stöd åt inbyggda anslutningsappar, konvertering av format, kolumnmappning och bättre och skalbar dataöverföring.
- **Aktivitetssändning:** Skicka och övervaka omvandlingsaktiviteter som körs på en mängd olika beräkningstjänster, till exempel Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server med mera.
- **SSIS paketkörning**: Internt köra SQL Server Integration Services-paket (SSIS) i en hanterad Azure-beräkningsmiljö.

I Data Factory definierar en aktivitet åtgärden som ska utföras. En länkad tjänst definierar ett datalager som mål eller en beräkningstjänst. Integration Runtime utgör bryggan mellan aktiviteten och länkade tjänster.  Den refereras av den länkade tjänsten eller aktiviteten och tillhandahåller beräkningsmiljön där aktiviteten antingen körs på eller skickas från. På så sätt kan aktiviteten utföras i regionen som är den närmaste möjliga till måldatalagret eller beräkningstjänsten på det bästa sättet samtidigt som den uppfyller säkerhets- och efterlevnadsbehoven.

## <a name="integration-runtime-types"></a>Integration Runtime

Data Factory erbjuder tre typer av Integration Runtime (IR), och du bör välja den typ som bäst tjänar de dataintegrationsfunktioner och nätverksmiljöbehov du letar efter.  Dessa tre typer är:

- Azure
- Egen värd
- Azure-SSIS

I följande tabell beskrivs funktioner och nätverksstöd för varje Integration Runtime-typ:

IR-typ | Offentligt nätverk | Privat nätverk
------- | -------------- | ---------------
Azure | Dataflöde<br/>Dataförflyttning<br/>Aktivitetssändning | &nbsp;
Egen värd | Dataförflyttning<br/>Aktivitetssändning | Dataförflyttning<br/>Aktivitetssändning
Azure-SSIS | Körning av SSIS-paket | Körning av SSIS-paket

Följande diagram visar hur olika IR-körningar kan användas i kombination för att ge omfattande dataintegrationsfunktioner och nätverksstöd:

![Olika typer av Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure Integration Runtime

En Azure-integreringskörning kan:

- Köra dataflöden i Azure 
- Köra kopieringsaktivitet mellan molndatalager
- Skickar följande transformeringsaktiviteter i det offentliga nätverket: Databricks Notebook/ Jar/ Python-aktivitet, HDInsight Hive-aktivitet, HDInsight Pig-aktivitet, HDInsight MapReduce-aktivitet, HDInsight Spark-aktivitet, HDInsight Streaming-aktivitet, Machine Learning Batch Execution-aktivitet, Machine Learning Update Resource-aktiviteter, Aktiviteter för lagrad procedur, DataSjöanalys U-SQL-aktivitet, .NET anpassad aktivitet, webbaktivitet, uppslagsaktivitet och hämta metadataaktivitet.

### <a name="azure-ir-network-environment"></a>Azure IR-nätverksmiljö

Azure Integration Runtime stöder anslutning till datalager och beräknar tjänster med offentliga slutpunkter. Använd Integration Runtime med egen värd för Azure Virtual Network-miljön.

### <a name="azure-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure IR
Med Azure Integration Runtime får du en helt hanterad, serverlös beräkning i Azure.  Du behöver inte oroa dig för infrastrukturetablering, programvaruinstallation, korrigering eller kapacitetsskalning.  Dessutom betalar du bara för den faktiska användningen.

Med Azure Integration Runtime får du interna beräkningsfunktioner för att flytta data mellan molndatalager på ett säkert, pålitligt sätt med höga prestanda.  Du kan ange hur många dataintegreringsenheter som ska användas för kopieringsaktiviteten, och beräkningsstorleken för Azure IR skalas elastiskt upp efter detta utan att du behöver justera storleken på Azure Integration Runtime. 

Aktivitetssändning är en lättåtgärd för att dirigera aktiviteten till målberäkningstjänsten, så det behövs inte skala upp beräkningsstorleken för det här scenariot.

Om du vill ha information om hur du skapar och konfigurerar en Azure IR kan du referera till hur du skapar och konfigurerar Azure IR under hur du går till. 

> [!NOTE] 
> Azure Integration runtime har egenskaper relaterade till Data Flow runtime, som definierar den underliggande beräkningsinfrastrukturen som skulle användas för att köra dataflödena på. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd

En IR med egen värd kan:

- Köra kopieringsaktivitet mellan molndatalager och ett datalager i privat nätverk.
- Skickar följande transformeringsaktiviteter mot beräkningsresurser i lokalt nätverk eller Azure Virtual Network: HDInsight Hive-aktivitet (BYOC-Bring Your Own Cluster), HDInsight Pig-aktivitet (BYOC), HDInsight MapReduce-aktivitet (BYOC), HDInsight Spark-aktivitet (BYOC), Machine Learning Batch Execution-aktivitet, Machine Learning Update Resource-aktiviteter, Aktiviteter för lagrad procedur, DataSjöanalys U-SQL-aktivitet, Anpassad aktivitet (körs på Azure Batch) , Uppslagsaktivitet och Hämta metadataaktivitet.

> [!NOTE] 
> Använd självvärd för integrationskörning för att stödja datalager som kräver bring-your-own-drivrutin som SAP Hana, MySQL, etc.  Mer information finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) är ett beroende av Self Hosted IR. Kontrollera att du har JRE installerat på samma värd.

### <a name="self-hosted-ir-network-environment"></a>IR-nätverksmiljö med egen värd

Om du vill utföra dataintegration på ett säkert sätt i en privat nätverksmiljö, som inte har en direkt siktlinje från den offentliga molnmiljön, kan du installera en självvärd IR i lokal miljö bakom företagets brandvägg eller i ett virtuellt privat nätverk.  IR med egen värd utför bara utgående HTTP-baserade anslutningar till öppet internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i IR med egen värd

Installera Självvärdbaserad IR på en lokal dator eller en virtuell dator i ett privat nätverk. För närvarande stöder vi bara att IR med egen värd körs på ett Windows-operativsystem.  

För hög tillgänglighet och skalbarhet kan du skala ut IR med egen värd genom att associera den logiska instansen med flera lokala datorer i aktiv/aktiv-läge.  Mer information finns i hur du [skapar och konfigurerar självvärd ir-artikel](create-self-hosted-integration-runtime.md) under hur du söker guider för mer information.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime

Om du vill lyfta och skifta befintlig SSIS-arbetsbelastning kan du skapa en Azure-SSIS IR för att köra SSIS-paket internt.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-nätverksmiljö

Azure-SSIS IR kan etableras i offentliga eller privata nätverk.  Åtkomst till lokala data stöds genom att koppla Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure-SSIS IR

Azure-SSIS IR är ett helt hanterat kluster av virtuella Azure-datorer särskilt avsedda att köras SSIS-paketen. Du kan ta med din egen Azure SQL Database- eller Managed Instance-server som värd för katalogen med SSIS-projekt/-paket (SSISDB) som ska kopplas till den. Du kan skala upp kraften i beräkningen genom att ange nodstorlek och skala ut den genom att ange antalet noder i klustret. Du kan hantera kostnaden för att köra Azure-SSIS Integration Runtime genom att stoppa och starta den efter önskemål.

Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar Azure-SSIS IR under ”så här gör du”-guiderna.  När du har skapat den kan du distribuera och hantera dina befintliga SSIS-paket med få eller inga ändringar med välbekanta verktyg, som SQL Server Data Tools (SSDT) och SQL Server Management Studio (SSMS), precis som när du använder SSIS lokalt.

Mer information om Azure-SSIS runtime finns i följande artiklar: 

- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller steg-för-steg-instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökar självstudien och innehåller instruktioner om hur du använder Azure SQL Database Managed Instance och ansluter till IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 

## <a name="integration-runtime-location"></a>Integration Runtime-plats

Data Factory-platsen är där metadata för datafabriken lagras och där utlösningen av pipeline initieras. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster. Det här beteende realiseras via [globalt tillgängligt IR](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksegress.

IR-platsen definierar platsen för backend-beräkningen och i stort sett platsen där dataflytt, aktivitetssändning och SSIS-paketkörning utförs. IR-platsen kan skilja sig från platsen som datafabriken tillhör. 

### <a name="azure-ir-location"></a>Azure IR-plats

- För kopieringsaktivitet gör ADF sitt bästa för att automatiskt identifiera sink-datalagrets plats och sedan använda IR i antingen samma region om den är tillgänglig eller den närmaste i samma geografi. Om sink-datalagrets region inte kan detekteras används IR i datafabriksregionen som alternativ.

  Till exempel har du din fabrik skapad i östra USA, 
  
  - När du kopierar data till Azure Blob i västra USA, om ADF har upptäckt att Blob är i västra USA, kopierar aktiviteten körs på IR i västra USA. Om regionidentifieringen misslyckas utförs kopieringsaktiviteten på IR i östra USA.
  - När du kopierar data till Salesforce som regionen inte kan identifieras för utförs kopieringsaktiviteten på IR i östra USA.

- För kopieringsaktivitet gör ADF det bästa för att automatiskt identifiera diskbänks- och källdatalagret för att välja den bästa platsen, antingen i samma region (om tillgängligt) eller den närmaste i samma geografi, eller om den inte kan identifieras för att använda datafabriksregionen som alternativ.

- För körning av uppslag/GetMetadata/Delete -aktivitet (kallas även Pipeline-aktiviteter), omvandlingsaktivitetssändning (kallas även externa aktiviteter) och redigeringsåtgärder (testanslutning, bläddrar i mapplista och tabelllista, förhandsgranskar data) använder ADF IR i datafabriksområdet.

- För dataflöde använder ADF IR i datafabriksregionen. 

  > [!TIP] 
  > En god praxis skulle vara att se till att dataflödet körs i samma region som motsvarande datalager (om möjligt). Du kan antingen uppnå detta genom att automatiskt lösa Azure IR (om datalagringsplatsen är samma som Data Factory-platsen) eller genom att skapa en ny Azure IR-instans i samma region som dina datalager och sedan köra dataflödet på den. 

Du kan övervaka vilken IR plats som börjar gälla under körning av aktiviteten i övervakningsvyn för pipeline-aktivitet i gränssnittet eller en aktivitetsövervakningsnyttolast.

### <a name="self-hosted-ir-location"></a>Namn på IR med egen värd

IR med egen värd registreras logiskt hos Data Factory och du får beräkningen som används för att stödja dess funktioner. Det finns därför ingen uttrycklig platsegenskapen för IR med egen värd. 

När IR med egen värd används för att utföra dataflyttning extraherar den data från källan och skriver dem till målet.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR-plats

Att välja rätt plats för Azure-SSIS IR är viktigt för att uppnå höga prestanda i dina arbetsflöden för extrahering, transformering och laddning (ETL).

- Platsen för din Azure-SSIS IR behöver inte vara samma som platsen för din datafabrik, men den bör vara samma som platsen för din egen Azure SQL-databas eller hanterade instansserver där SSISDB ska vara värd. På så sätt kan din Azure-SSIS Integration Runtime enkelt få åtkomst till SSISDB utan att det medför överdriven trafik mellan olika platser.
- Om du inte har en befintlig Azure SQL Database- eller Managed Instance-server som värd för SSISDB, men du har lokala datakällor/-mål, bör du skapa en ny Azure SQL Database- eller Managed Instance-server på samma plats som ett virtuellt nätverk som är anslutet till ditt lokala nätverk.  På så sätt kan du skapa din Azure-SSIS IR med den nya Azure SQL Database eller Managed Instance-servern och gå med i det virtuella nätverket, alla på samma plats, vilket effektivt minimerar dataförflyttningar på olika platser.
- Om platsen för din befintliga Azure SQL Database- eller Managed Instance-server där SSISDB finns inte är samma som platsen för ett virtuellt nätverk som är anslutet till ditt lokala nätverk, skapar du först din Azure-SSIS IR med hjälp av en befintlig Azure SQL Database- eller Managed Instance-server och ansluter till ett annat virtuellt nätverk på samma plats och konfigurera sedan ett virtuellt nätverk till virtuell nätverksanslutning mellan olika platser.

I följande diagram visas platsinställningar för Data Factory och dess Integration Runtime-instanser:

![Integration Runtime-plats](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bestämma vilken IR som ska användas

### <a name="copy-activity"></a>Kopieringsaktivitet

För kopieringsaktiviteten kräver den länkade tjänster-källa och länkade tjänster-mottagare för att definiera dataflödets riktning. Följande logik används till att bestämma vilken Integration Runtime-instans som används för att utföra kopieringen: 

- **Kopiera mellan två molndatakällor**: när både käll- och diskhoslänkade tjänster använder Azure IR använder ADF den regionala Azure IR om du har angett, eller automatiskt bestämma en plats för Azure IR om du väljer autoresolve IR (standard) enligt beskrivningen i avsnittet [Integration-körningsplats.](#integration-runtime-location)
- **Kopiera mellan molndatalager och datakälla i privat nätverk**: om den länkade källtjänsten eller den länkade mottagartjänsten pekar på en IR med egen värd körs kopieringsaktiviteten på den Integration Runtime med egen värd.
- **Kopiering mellan två datakällor i privat nätverk**: både källan och diskbänkens länkade tjänst måste peka på samma instans av integrationskörning och att integrationskörning används för att köra kopieringen Aktivitet.

### <a name="lookup-and-getmetadata-activity"></a>Lookup och GetMetadata-aktivitet

Aktiviteterna Lookup och GetMetadata har körts på integreringskörningsmiljön som är associerad med den länkade datalagringstjänsten.

### <a name="external-transformation-activity"></a>Extern omvandlingsaktivitet

Varje extern omvandlingsaktivitet som använder en extern beräkningsmotor har en målberäkning länkad tjänst, som pekar på en integrationskörning. Den här integrationskörningsinstansen bestämmer platsen där den externa handkodade omvandlingsaktiviteten skickas från.

### <a name="data-flow-activity"></a>Aktivitet för dataflöde

Dataflödesaktiviteter körs på Azure-integreringskörningen som är associerad med den. Spark-beräkningen som används av dataflöden bestäms av dataflödesegenskaperna i din Azure Integration Runtime och hanteras fullständigt av ADF.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Skapa Azure-integreringskörning](create-azure-integration-runtime.md)
- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökar självstudien och innehåller instruktioner om hur du använder Azure SQL Database Managed Instance och ansluter till IR till ett virtuellt nätverk. 
