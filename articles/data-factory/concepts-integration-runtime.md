---
title: Integration Runtime i Azure Data Factory | Microsoft Docs
description: Mer information om Integration Runtime i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 91225136926427fb22c80bc2e198c153c60614eb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298142"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime i Azure Data Factory
Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att ge olika nätverksmiljöer integrationsfunktioner:

- **Dataflytt**: Flytta data mellan datalager i offentliga nätverk och datalager i privata nätverk (lokalt eller virtuellt privat nätverk). Den ger stöd åt inbyggda anslutningsappar, konvertering av format, kolumnmappning och bättre och skalbar dataöverföring.
- **Aktivitetssändning**: Skick och övervaka transformeringsaktiviteter som körs på en mängd olika beräkningstjänster som Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server med flera.
- **SSIS paketkörning**: Internt köra SQL Server Integration Services-paket (SSIS) i en hanterad Azure-beräkningsmiljö.


> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

I Data Factory definierar en aktivitet åtgärden som ska utföras. En länkad tjänst definierar ett datalager som mål eller en beräkningstjänst. Integration Runtime utgör bryggan mellan aktiviteten och länkade tjänster.  Den refereras av den länkade tjänsten och tillhandahåller beräkningsmiljön där aktiviteten körs eller skickas från.  På så sätt kan aktiviteten utföras i regionen som är den närmaste möjliga till måldatalagret eller beräkningstjänsten på det bästa sättet samtidigt som den uppfyller säkerhets- och efterlevnadsbehoven.

## <a name="integration-runtime-types"></a>Integration Runtime
Data Factory erbjuder tre typer av Integration Runtime och du bör välja den typ som fungerar bäst med dataintegreringstjänsterna och de nätverksmiljöbehov du har.  Dessa tre typer är:

- Azure
- Egen värd
- Azure-SSIS

I följande tabell beskrivs funktioner och nätverksstöd för varje Integration Runtime-typ:

IR-typ | Offentligt nätverk | Privat nätverk
------- | -------------- | ---------------
Azure | Dataförflyttning<br/>Aktivitetssändning | &nbsp;
Egen värd | Dataförflyttning<br/>Aktivitetssändning | Dataförflyttning<br/>Aktivitetssändning
Azure-SSIS | Körning av SSIS-paket | Körning av SSIS-paket

Följande diagram visar hur olika IR-körningar kan användas i kombination för att ge omfattande dataintegrationsfunktioner och nätverksstöd:

![Olika typer av Integration Runtime](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Azure Integration Runtime
En Azure Integration Runtime kan:

- Köra kopieringsaktivitet mellan molndatalager
- Skicka följande transformeringsaktiviteter i offentligt nätverk: HDInsight Hive-aktivitet, HDInsight Pig-aktivitet, HDInsight MapReduce-aktivitet, HDInsight Spark-aktivitet, HDInsight Streaming-aktivitet, Machine Learning-batchkörningsaktivitet, Machine Learning-resursuppdateringsaktiviteter, lagrad procedur-aktivitet, Data Lake Analytics U-SQL-aktivitet, egen .Net-aktivitet, Web-aktivitet, Lookup-aktivitet och GetMetadata-aktivitet.

### <a name="network-environment"></a>Nätverksmiljö
Azure Integration Runtime stöder anslutning till datalager och beräkningstjänster i offentligt nätverk med offentligt tillgängliga slutpunkter. Använd Integration Runtime med egen värd för Azure Virtual Network-miljön.

### <a name="compute-resource-and-scaling"></a>Beräkningsresurs och skalning
Med Azure Integration Runtime får du en helt hanterad, serverlös beräkning i Azure.  Du behöver inte bekymra dig om infrastrukturetablering, programvaruinstallation, uppdatering eller skalbarhet.  Dessutom betalar du bara för den faktiska användningen.

Med Azure Integration Runtime får du interna beräkningsfunktioner för att flytta data mellan molndatalager på ett säkert, pålitligt sätt med höga prestanda.  Du kan ange hur många dataflyttenheter som ska användas för kopieringsaktiviteten, och beräkningsstorleken för Azure IR skalas elastiskt upp efter detta utan att du behöver justera storleken på Azure Integration Runtime.

Aktivitetssändning är en enkel åtgärd för att dirigera aktiviteten till målberäkningsjänsten, så du behöver inte skala upp beräkningsstorleken för det här scenariot.

Information om hur du skapar och konfigurerar Azure IR finns i How to create and configure Azure IR (Så här skapar och konfigurerar du Azure IR) och under ”så här gör du”-guiderna. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd
En IR med egen värd kan:

- Köra kopieringsaktivitet mellan molndatalager och ett datalager i privat nätverk.
- Skicka följande transformeringsaktiviteter mot beräkningsresurser i lokalt nätverk eller Azure Virtual Network: HDInsight Hive-aktivitet (BYOC), HDInsight Pig-aktivitet (BYOC), HDInsight MapReduce-aktivitet (BYOC), HDInsight Spark-aktivitet (BYOC), HDInsight Streaming-aktivitet (BYOC), Machine Learning-batchkörningsaktivitet, Machine Learning-resursuppdateringsaktiviteter, lagrad procedur-aktivitet, Data Lake Analytics U-SQL-aktivitet, egen .Net-aktivitet, Lookup-aktivitet och GetMetadata-aktivitet.

> [!NOTE] 
> Använda Integration Runtime med egen värd för att stödja datalager som kräver egen drivrutin, till exempel SAP Hana, MySQL osv.  Mer information finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Nätverksmiljö
Om du vill utföra dataintegration säkert i en privat nätverk-miljö, som har skymd sikt från offentligt moln-miljön, kan du installera IR med egen värd i en lokal miljö bakom företagsbrandväggen, eller i ett virtuellt privat nätverk.  IR med egen värd utför bara utgående HTTP-baserade anslutningar till öppet internet.

### <a name="compute-resource-and-scaling"></a>Beräkningsresurs och skalning
IR med egen värd måste installeras på en lokal dator eller en virtuell dator i ett privat nätverk. För närvarande stöder vi bara att IR med egen värd körs på ett Windows-operativsystem.  

För hög tillgänglighet och skalbarhet kan du skala ut IR med egen värd genom att associera den logiska instansen med flera lokala datorer i aktiv/aktiv-läge.  Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar IR med egen värd under ”så här gör du”-guiderna.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Om du vill lyfta och skifta befintlig SSIS-arbetsbelastning kan du skapa en Azure-SSIS IR för att köra SSIS-paket internt.

### <a name="network-environment"></a>Nätverksmiljö
Azure-SSIS IR kan etableras i offentliga eller privata nätverk.  Åtkomst till lokala data stöds genom att koppla Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket.  

### <a name="compute-resource-and-scaling"></a>Beräkningsresurs och skalning
Azure-SSIS IR är ett helt hanterat kluster av virtuella Azure-datorer särskilt avsedda att köras SSIS-paketen. Du kan använda en egen Azure SQL Database- eller hanterad instans-server (förhandsversion) som värd för katalogen med SSIS-projekt/-paket (SSISDB) som kommer att kopplas till den. Du kan skala upp kraften i beräkningen genom att ange nodstorlek och skala ut den genom att ange antalet noder i klustret. Du kan hantera kostnaden för att köra Azure-SSIS Integration Runtime genom att stoppa och starta den efter önskemål.

Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar Azure-SSIS IR under ”så här gör du”-guiderna.  När du har skapat den kan du distribuera och hantera dina befintliga SSIS-paket med få eller inga ändringar med välbekanta verktyg, som SQL Server Data Tools (SSDT) och SQL Server Management Studio (SSMS), precis som när du använder SSIS lokalt.

Mer information om Azure-SSIS runtime finns i följande artiklar: 

- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL-hanterade instanser (förhandsversion) och ansluter IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 

## <a name="determining-which-ir-to-use"></a>Bestämma vilken IR som ska användas
Varje transformeringsaktivitet har en länkad målberäkningstjänst, som pekar på en Integration Runtime. Denna Integration Runtime-instans är där transformeringsaktiviteten skickas från.

För kopieringsaktiviteten kräver den länkade tjänster-källa och länkade tjänster-mottagare för att definiera dataflödets riktning. Följande logik används till att bestämma vilken Integration Runtime-instans som används för att utföra kopieringen: 

- **Kopiera mellan två molndatalager**: när länkade tjänster-källan och länkade tjänster-mottagaren använder Azure IR används IR som används av den länkade mottagartjänsten för att köra kopieringsaktiviteten.
- **Kopiera mellan molndatalager och datakälla i privat nätverk**: om den länkade källtjänsten eller den länkade mottagartjänsten pekar på en IR med egen värd körs kopieringsaktiviteten på den Integration Runtime med egen värd.
- **Kopiera mellan två datakällor i privat nätverk**: Båda den länkade käll- och mottagartjänsten måste peka på samma instans av Integration Runtime, och den IR används för att köra kopieringsaktiviteten.

I följande diagram visas två exempel på kopieringsaktivitet:

- För kopieringsaktivitet 1 är källan en länkad SQL Server-tjänst som refererar till IR med egen värd A, och dess mottagare är en länkad Azure Storage-tjänst som refererar till Azure IR B. När kopieringsaktiviteten utförs så körs den på IR med egen värd A.
- För kopieringsaktivitet 2 är källan en länkad Azure SQL Database-tjänst som refererar till Azure IR C, och dess mottagare är en länkad Azure Storage-tjänst som refererar till Azure IR B. När kopieringsaktiviteten utförs så körs den på Azure IR B eftersom det är den Integration Runtime som används av den länkade mottagartjänsten.

![Vilken IR som ska användas](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Integration Runtime-plats
Data Factory-platsen är där metadata för datafabriken lagras och där utlösningen av pipeline initieras. De Data Factory-platser som för närvarande stöds är: USA, östra; USA, östra 2; Asien, sydöstra och Europa, västra. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster. Det här beteende realiseras via globalt tillgängligt IR i flera regioner för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksegress.

IR-platsen definierar platsen för backend-beräkningen och i stort sett platsen där dataflytt, aktivitetssändning och SSIS-paketkörning utförs. IR-platsen kan skilja sig från platsen som datafabriken tillhör. I följande diagram visas platsinställningar för Data Factory och dess Integration Runtime-instanser:

![Integration Runtime-plats](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure IR
Data Factory använder en Azure IR i den region som ligger närmast mottagaren på samma geografiska plats för att flytta data. Se tabellen nedan för mappning:

Geografisk plats för mottagardatalagret | Plats för mottagardatalagret | Plats som används för Azure Integration Runtime
-------------------------------| ----------------| ------------------
USA | Östra USA | Östra USA
&nbsp; | Östra USA 2 | Östra USA 2
&nbsp; | Centrala USA | Centrala USA
&nbsp; | Norra centrala USA | Norra centrala USA
&nbsp; | Södra centrala USA | Södra centrala USA
&nbsp; | Västra centrala USA | Västra centrala USA
&nbsp; | Västra USA | Västra USA
&nbsp; | Västra USA 2 | Västra USA 2
Kanada | Östra Kanada | Centrala Kanada
&nbsp; | Centrala Kanada | Centrala Kanada
Brasilien | Södra Brasilien | Södra Brasilien
Europa | Norra Europa | Norra Europa
&nbsp; | Västra Europa | Västra Europa
Storbritannien | Storbritannien, västra | Storbritannien, södra
&nbsp; | Storbritannien, södra | Storbritannien, södra
Asien och stillahavsområdet | Sydostasien | Sydostasien
&nbsp; | Östasien | Sydostasien
Australien | Östra Australien | Östra Australien
&nbsp; | Sydöstra Australien | Sydöstra Australien
Japan | Östra Japan | Östra Japan
&nbsp; | Västra Japan | Östra Japan
Korea | Centrala Korea | Centrala Korea
&nbsp; | Sydkorea | Centrala Korea
Indien | Indien, centrala | Indien, centrala
&nbsp; | Indien, västra | Indien, centrala
&nbsp; | Södra Indien | Indien, centrala

Du kan också ange platsen för en Azure IR för automatisk lösning, vilket innebär att Data Factory automatiskt försöker hitta den bästa platsen att använda baserat på den länkade tjänstens definition.

> [!NOTE] 
> Om regionen för måldatalagret inte finns i listan eller inte kan identifieras misslyckas aktiviteten istället för att gå igenom en alternativ region av kompatibilitetsskäl. I det här fallet anger du uttryckligen den alternativa platsen som ska användas för att utföra kopieringen.
 
I följande bild visas ett exempel på den gällande platsen när platsen för Azure IR ställs in på automatisk lösning. När en kopieringsaktivitet körs identifierar den platsen för datamålet, i det här exemplet är det västra Japan.  Baserat på tabellen används en Azure IR i östra för att utföra den faktiska kopieringen. När samma IR används till att ansluta till HDInsight för en Spark-aktivitet sker Spark-programsändningen från Data Factory-platsen, i det här exemplet är det östra USA, och den faktiska körningen av Spark-programmet sker på HDInsight-serverplatsen. 

![Gällande plats](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>IR med egen värd
IR med egen värd registreras logiskt hos Data Factory och du får beräkningen som används för att stödja dess funktioner. Det finns därför ingen uttrycklig platsegenskapen för IR med egen värd. 

När IR med egen värd används för att utföra dataflyttning extraherar den data från källan och skriver dem till målet.

### <a name="azure-ssis-ir"></a>Azure-SSIS IR
Att välja rätt plats för Azure-SSIS IR är viktigt för att uppnå höga prestanda i dina arbetsflöden för extrahering, transformering och laddning (ETL).  Sex platser är tillgängliga initialt för förhandsversionen (USA, östra; USA, östra 2; USA, centrala; Australien, östra; Europa, norra och Europa, västra).

- Platsen för din Azure-SSIS IR måste inte vara samma som platsen för din datafabrik, men den bör vara samma som platsen för din egen Azure SQL Database-server/hanterad instans-server (förhandsversion) där SSISDB ska finnas. På så sätt kan din Azure-SSIS Integration Runtime enkelt få åtkomst till SSISDB utan att det medför överdriven trafik mellan olika platser.
- Om du inte har en befintlig Azure SQL Database-server/hanterad instans-server (förhandsversion) som värd för SSISDB, men du har lokala datakällor/-mål, bör du skapa en ny Azure SQL Database-server/hanterad instans-server (förhandsversion) på samma plats som ett virtuellt nätverk som är anslutet till ditt lokala nätverk.  På så sätt kan du skapa din Azure-SSIS IR med den nya Azure SQL Database-servern/hanterad instans-servern (förhandsversion) och koppla aktuellt virtuellt nätverk, allt på samma plats, vilket effektivt minimerar dataflyttningar mellan olika platser.
- Om platsen för din befintliga Azure SQL Database-server/hanterad instans-server (förhandsversion) där SSISDB finns inte är samma plats som platsen för ett virtuellt nätverk som är anslutet till ditt lokala nätverk, skapar du först din Azure-SSIS IR med en befintlig Azure SQL Database-server/hanterad instans-server (förhandsversion) och kopplar ett annat virtuellt nätverk på samma plats. Sedan konfigurerar du en VNet-till-VNet-anslutning mellan olika platser.


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL-hanterade instanser (förhandsversion) och ansluter IR till ett virtuellt nätverk. 
