---
title: Integration Runtime i Azure Data Factory
description: Mer information om Integration Runtime i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218817"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime i Azure Data Factory
Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att ge olika nätverksmiljöer integrationsfunktioner:

- **Data Flow**: Execute a [Data Flow](concepts-data-flow-overview.md) in managed Azure compute environment.  
- **Data movement**: Copy data across data stores in public network and data stores in private network (on-premises or virtual private network). Den ger stöd åt inbyggda anslutningsappar, konvertering av format, kolumnmappning och bättre och skalbar dataöverföring.
- **Activity dispatch**:  Dispatch and monitor transformation activities running on a variety of compute services such as Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, and more.
- **SSIS paketkörning**: Internt köra SQL Server Integration Services-paket (SSIS) i en hanterad Azure-beräkningsmiljö.

I Data Factory definierar en aktivitet åtgärden som ska utföras. En länkad tjänst definierar ett datalager som mål eller en beräkningstjänst. Integration Runtime utgör bryggan mellan aktiviteten och länkade tjänster.  It is referenced by the linked service or activity, and provides the compute environment where the activity either runs on or gets dispatched from. På så sätt kan aktiviteten utföras i regionen som är den närmaste möjliga till måldatalagret eller beräkningstjänsten på det bästa sättet samtidigt som den uppfyller säkerhets- och efterlevnadsbehoven.

## <a name="integration-runtime-types"></a>Integration Runtime
Data Factory erbjuder tre typer av Integration Runtime och du bör välja den typ som fungerar bäst med dataintegreringstjänsterna och de nätverksmiljöbehov du har.  Dessa tre typer är:

- Azure
- Egen värd
- Azure-SSIS

I följande tabell beskrivs funktioner och nätverksstöd för varje Integration Runtime-typ:

IR-typ | Offentligt nätverk | Privat nätverk
------- | -------------- | ---------------
Azure | Data Flow<br/>Dataförflyttning<br/>Aktivitetssändning | &nbsp;
Egen värd | Dataförflyttning<br/>Aktivitetssändning | Dataförflyttning<br/>Aktivitetssändning
Azure-SSIS | Körning av SSIS-paket | Körning av SSIS-paket

Följande diagram visar hur olika IR-körningar kan användas i kombination för att ge omfattande dataintegrationsfunktioner och nätverksstöd:

![Olika typer av Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
En Azure Integration Runtime kan:

- Running Data Flows in Azure 
- Köra kopieringsaktivitet mellan molndatalager
- Dispatching the following transform activities in public network: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, and Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Azure IR-nätverksmiljö
Azure Integration Runtime supports connecting to data stores and compute services with public accessible endpoints. Använd Integration Runtime med egen värd för Azure Virtual Network-miljön.

### <a name="azure-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure IR
Med Azure Integration Runtime får du en helt hanterad, serverlös beräkning i Azure.  Du behöver inte bekymra dig om infrastrukturetablering, programvaruinstallation, uppdatering eller skalbarhet.  Dessutom betalar du bara för den faktiska användningen.

Med Azure Integration Runtime får du interna beräkningsfunktioner för att flytta data mellan molndatalager på ett säkert, pålitligt sätt med höga prestanda.  Du kan ange hur många dataintegreringsenheter som ska användas för kopieringsaktiviteten, och beräkningsstorleken för Azure IR skalas elastiskt upp efter detta utan att du behöver justera storleken på Azure Integration Runtime. 

Aktivitetssändning är en enkel åtgärd för att dirigera aktiviteten till målberäkningstjänsten, så du behöver inte skala upp beräkningsstorleken för det här scenariot.

Information om hur du skapar och konfigurerar Azure IR finns i How to create and configure Azure IR (Så här skapar och konfigurerar du Azure IR) och under ”så här gör du”-guiderna. 

> [!NOTE] 
> Azure Integration runtime has properties related to Data Flow runtime, which defines the underlying compute infrastructure that would be used to run the data flows on. 

## <a name="self-hosted-integration-runtime"></a>Lokalt installerad integrationskörning
En IR med egen värd kan:

- Köra kopieringsaktivitet mellan molndatalager och ett datalager i privat nätverk.
- Dispatching the following transform activities against compute resources in on-premises or Azure Virtual Network: HDInsight Hive activity (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activity (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch), Lookup activity, and Get Metadata activity.

> [!NOTE] 
> Use self-hosted integration runtime to support data stores that requires bring-your-own driver such as SAP Hana, MySQL, etc.  For more information, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) is a dependency of Self Hosted IR. Please make sure you have JRE installed on the same host.

### <a name="self-hosted-ir-network-environment"></a>IR-nätverksmiljö med egen värd
Om du vill utföra dataintegration säkert i en privat nätverk-miljö, som har skymd sikt från offentligt moln-miljön, kan du installera IR med egen värd i en lokal miljö bakom företagsbrandväggen, eller i ett virtuellt privat nätverk.  IR med egen värd utför bara utgående HTTP-baserade anslutningar till öppet internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i IR med egen värd
IR med egen värd måste installeras på en lokal dator eller en virtuell dator i ett privat nätverk. För närvarande stöder vi bara att IR med egen värd körs på ett Windows-operativsystem.  

För hög tillgänglighet och skalbarhet kan du skala ut IR med egen värd genom att associera den logiska instansen med flera lokala datorer i aktiv/aktiv-läge.  For more information, see how to [create and configure self-hosted IR](create-self-hosted-integration-runtime.md) article under how to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Om du vill lyfta och skifta befintlig SSIS-arbetsbelastning kan du skapa en Azure-SSIS IR för att köra SSIS-paket internt.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-nätverksmiljö
Azure-SSIS IR kan etableras i offentliga eller privata nätverk.  Åtkomst till lokala data stöds genom att koppla Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure-SSIS IR
Azure-SSIS IR är ett helt hanterat kluster av virtuella Azure-datorer särskilt avsedda att köras SSIS-paketen. You can bring your own Azure SQL Database or Managed Instance server to host the catalog of SSIS projects/packages (SSISDB) that is going to be attached to it. Du kan skala upp kraften i beräkningen genom att ange nodstorlek och skala ut den genom att ange antalet noder i klustret. Du kan hantera kostnaden för att köra Azure-SSIS Integration Runtime genom att stoppa och starta den efter önskemål.

Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar Azure-SSIS IR under ”så här gör du”-guiderna.  När du har skapat den kan du distribuera och hantera dina befintliga SSIS-paket med få eller inga ändringar med välbekanta verktyg, som SQL Server Data Tools (SSDT) och SQL Server Management Studio (SSMS), precis som när du använder SSIS lokalt.

Mer information om Azure-SSIS runtime finns i följande artiklar: 

- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 

## <a name="integration-runtime-location"></a>Integration Runtime-plats
Data Factory-platsen är där metadata för datafabriken lagras och där utlösningen av pipeline initieras. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster. Det här beteende realiseras via [globalt tillgängligt IR](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksegress.

IR-platsen definierar platsen för backend-beräkningen och i stort sett platsen där dataflytt, aktivitetssändning och SSIS-paketkörning utförs. IR-platsen kan skilja sig från platsen som datafabriken tillhör. 

### <a name="azure-ir-location"></a>Azure IR-plats
Du kan ställa in en vissa plats för en Azure IR varmed dataflytter eller aktivitetssändningar sker i den specifika regionen. 

If you choose to use the **auto-resolve Azure IR** which is the default, 

- För kopieringsaktivitet kommer ADF att försöka identifiera ditt mål- och källdatalager för att välja den bästa platsen, antingen i samma region om den är tillgänglig eller den närmaste inom samma geografiska område. Om det inte går att avgöra används datafabriksregionen.

- For Lookup/GetMetadata/Delete activity execution (also known as Pipeline activities), transformation activity dispatching (also known as External activities), and authoring operations (test connection, browse folder list and table list, preview data), ADF will use the IR in the data factory region.

- For Data Flow, ADF will use the IR in the data factory region. 

  > [!TIP] 
  > A good practice would be to ensure Data flow runs in the same region as your corresponding data stores (if possible). You can either achieve this by auto-resolve Azure IR (if data store location is same as Data Factory location), or by creating a new Azure IR instance in the same region as your data stores and then execute the data flow on it. 

Du kan övervaka vilken IR plats som börjar gälla under körning av aktiviteten i övervakningsvyn för pipeline-aktivitet i gränssnittet eller en aktivitetsövervakningsnyttolast.

>[!TIP]
>Om du måste efterleva strikta datakrav och måste säkerställa att data inte lämnar ett visst område kan du uttryckligen skapa en Azure IR i den regionen och hänvisa den länkade tjänsten till denna IR med egenskapen ConnectVia. Till exempel om du vill kopiera data från en blob i Storbritannien, Syd, till SQL DW i Storbritannien, Syd, och vill se till att data inte lämnar Storbritannien ska du skapa en Azure-IR i Storbritannien, Syd, och länka båda länkade tjänster till denna IR.

### <a name="self-hosted-ir-location"></a>Namn på IR med egen värd
IR med egen värd registreras logiskt hos Data Factory och du får beräkningen som används för att stödja dess funktioner. Det finns därför ingen uttrycklig platsegenskapen för IR med egen värd. 

När IR med egen värd används för att utföra dataflyttning extraherar den data från källan och skriver dem till målet.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR-plats
Att välja rätt plats för Azure-SSIS IR är viktigt för att uppnå höga prestanda i dina arbetsflöden för extrahering, transformering och laddning (ETL).

- The location of your Azure-SSIS IR does not need be the same as the location of your data factory, but it should be the same as the location of your own Azure SQL Database/Managed Instance server where SSISDB is to be hosted. På så sätt kan din Azure-SSIS Integration Runtime enkelt få åtkomst till SSISDB utan att det medför överdriven trafik mellan olika platser.
- If you do not have an existing Azure SQL Database/Managed Instance server to host SSISDB, but you have on-premises data sources/destinations, you should create a new Azure SQL Database/Managed Instance server in the same location of a virtual network connected to your on-premises network.  This way, you can create your Azure-SSIS IR using the new Azure SQL Database/Managed Instance server and joining that virtual network, all in the same location, effectively minimizing data movements across different locations.
- If the location of your existing Azure SQL Database/Managed Instance server where SSISDB is hosted is not the same as the location of a virtual network connected to your on-premises network, first create your Azure-SSIS IR using an existing Azure SQL Database/Managed Instance server and joining another virtual network in the same location, and then configure a virtual network to virtual network connection between different locations.

I följande diagram visas platsinställningar för Data Factory och dess Integration Runtime-instanser:

![Integration Runtime-plats](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bestämma vilken IR som ska användas

### <a name="copy-activity"></a>Kopieringsaktivitet

För kopieringsaktiviteten kräver den länkade tjänster-källa och länkade tjänster-mottagare för att definiera dataflödets riktning. Följande logik används till att bestämma vilken Integration Runtime-instans som används för att utföra kopieringen: 

- **Kopiera mellan två molndatakällor**: när både käll- och mottagarnoderna för länkade tjänster använder Azure IR, använder ADF en regional Azure IR om du har angett eller automatiskt bestämt en plats för Azure IR om du väljer lös IR automatiskt (standard), enligt beskrivningen i avsnittet [Plats för integreringskörning](#integration-runtime-location).
- **Kopiera mellan molndatalager och datakälla i privat nätverk**: om den länkade källtjänsten eller den länkade mottagartjänsten pekar på en IR med egen värd körs kopieringsaktiviteten på den Integration Runtime med egen värd.
- **Kopiera mellan två datakällor i privat nätverk**: Båda den länkade käll- och mottagartjänsten måste peka på samma instans av Integration Runtime, och den IR används för att köra kopieringsaktiviteten.

### <a name="lookup-and-getmetadata-activity"></a>Lookup och GetMetadata-aktivitet

Aktiviteterna Lookup och GetMetadata har körts på integreringskörningsmiljön som är associerad med den länkade datalagringstjänsten.

### <a name="transformation-activity"></a>Transformeringsaktivitet

Varje transformeringsaktivitet har en länkad målberäkningstjänst, som pekar på en Integration Runtime. Denna Integration Runtime-instans är där transformeringsaktiviteten skickas från.

### <a name="data-flow-activity"></a>Data Flow activity

Data Flow activity is executed on the integration runtime associated to it. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Create Azure integration runtime](create-azure-integration-runtime.md)
- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
