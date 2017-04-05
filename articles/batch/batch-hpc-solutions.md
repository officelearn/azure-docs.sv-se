---
title: "Batch- och HPC-lösningar i molnet – Azure| Microsoft Docs"
description: "Läs mer om scenarier och lösningsalternativ för databehandling med höga prestanda (HCP och Big Compute) i Azure"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0bab12648a31b416bd7ceca75f0f83f5da226bad
ms.lasthandoff: 03/31/2017


---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Batch- och HPC-lösningar för storskaliga beräkningsarbetsbelastningar

Azure erbjuder effektiva, skalbara molnlösningar för Batch och HCP (High Performance Computing – databehandling med höga prestanda), även kallat *Big Compute*. Lär dig mer om Big Compute-arbetsbelastningar och Azures tjänster som stöder dem, eller gå direkt till [lösningsscenarierna](#scenarios) längre fram i den här artikeln. Den här artikeln är främst avsedd för tekniska beslutsfattare, IT-chefer och oberoende programvaruleverantörer, men även andra IT-experter och utvecklare kan ha nytta av den för att bekanta sig med dessa lösningar.

Många organisationer har problem med storskalig databearbetning, till exempel inom områden som teknisk design och analys, bildrendering, komplex modellering, Monte Carlo-simuleringar, beräkning av finansiella risker med mera. Azure hjälper organisationer att lösa dessa problem med de resurser, den skalning och det schema som de behöver. Med Azure kan organisationer:

* Skapa hybridlösningar och utöka ett lokalt HPC-kluster för att avlasta tunga arbetsbelastningar till molnet.
* Köra verktyg för HPC-kluster och arbetsbelastningar helt i Azure.
* Använda hanterade och skalbara Azure-tjänster som [Batch](https://azure.microsoft.com/documentation/services/batch/) för att köra beräkningsintensiva arbetsbelastningar utan att behöva distribuera och hantera beräkningsinfrastrukturen.

Även om det inte beskrivs i den här artikeln så innehåller Azure också en fullständig uppsättning funktioner, arkitekturalternativ och utvecklingsverktyg som utvecklare och partner kan använda för att skapa storskaliga, anpassade Big Compute-arbetsflöden. Ett växande partnerekosystem står redo att hjälpa dig att göra dina Big Compute-arbetsbelastningar mer produktiva i Azure-molnet.

## <a name="batch-and-hpc-applications"></a>Batch- och HPC-program
Till skillnad från webbprogram och många affärsapplikationer har Batch- och HCP-program en definierad starttid och sluttid, och de kan köras enligt ett schema eller på begäran, ibland i flera timmar eller längre. De flesta hör till någon av två kategorier: *parallella*, som kan köras parallellt på flera datorer eller processorer för att lösa problem, och *nära kopplade*. Följande tabell innehåller mer information om dessa programtyper. Vissa Azure-lösningsmetoder fungerar bättre med den ena eller andra typen.

> [!NOTE]
> I Batch- och HPC-lösningar kallas ofta en instans av ett program som körs för ett *jobb*, och varje jobb kan delas in i *aktiviteter*. Och de klustrade beräkningsresurserna för programmet brukar kallas för *beräkningsnoder*.
> 
> 

| Typ | Egenskaper | Exempel |
| --- | --- | --- |
| **Parallella**<br/><br/>![Parallella][parallel] |• Enskilda datorer kör programlogiken oberoende av varandra.<br/><br/> • Genom att fler datorer läggs till kan programmet skalas och beräkningstiden förkortas.<br/><br/>• Programmet består av separata körbara filer eller är uppdelat i en grupp med tjänster som anropas av en klient (ett SOA-program). |• Finansiell riskmodellering.<br/><br/>• Bildåtergivning och bildrendering.<br/><br/>• Mediekodning och transkodning.<br/><br/>• Monte Carlo-simuleringar.<br/><br/>• Programvarutestning. |
| **Nära kopplade**<br/><br/>![Nära kopplade][coupled] |• Programmet kräver beräkningsnoder för att interagera eller utbyta resultat.<br/><br/>• Beräkningsnoder kan kommunicera med MPI (Message Passing Interface), ett vanligt kommunikationsprotokoll för parallell datorbearbetning.<br/><br/>• Programmet är känslig för svarstider och bandbredd i nätverket.<br/><br/>• Programmets prestanda kan förbättras genom användning av tekniker för höghastighetsnätverk, t.ex. InfiniBand och direktåtkomst till fjärrminne (RDMA) |• Modellering av olje- och gasreservoarer.<br/><br/>• Teknisk design och analys, till exempel beräkningsströmningsdynamik.<br/><br/>• Fysiska simuleringar, till exempel bilkraschar och kärnreaktioner.<br/><br/>• Väderprognoser. |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Att tänka på när du kör Batch- och HPC-program i molnet
Du kan lätt migrera många program som är utformade att köras i lokala HPC-kluster till Azure, eller till en hybridmiljö (mellan lokala system). Det kan dock finnas vissa begränsningar eller saker som du måste ta hänsyn till, t.ex.:

* **Molnresursernas tillgänglighet** – Beroende på vilken typ av molnberäkningsresurser som du använder kanske du inte kan räkna med kontinuerlig datortillgänglighet medan ett jobb körs. Statushantering och förloppskontroller är vanliga tekniker för att hantera eventuella tillfälliga fel och är ännu viktigare när molnresurser används.
* **Dataåtkomst** – Dataåtkomstteknikerna som är allmänt tillgängliga i Enterprise-kluster, till exempel NFS, kan kräva särskilt konfiguration i molnet. Eller också måste du använda andra dataåtkomstmetoder och molnmönster.
* **Dataflyttning** – För program som bearbetar stora mängder data krävs strategier för att flytta data till molnet och för att beräkna resurser. Du kan behöva höghastighetsnätverk för lokala system, till exempel [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Du måste också ta hänsyn till eventuella juridiska krav, föreskrifter och policybegränsningar för lagringen av och åtkomsten till dessa data.
* **Licensiering** – Fråga leverantören av kommersiella program om licensiering krävs eller om det finns andra begränsningar för att köra i molnet. Alla leverantörer erbjuder inte licensiering enligt modellen Betala per användning. Du kan behöva planera för en licensserver i molnet för din lösning eller ansluta till en lokal licensserver.

### <a name="big-compute-or-big-data"></a>Big Compute eller stordata?
Skillnaden mellan Big Compute- och stordataprogram är inte alltid tydlig, och vissa program kan ha egenskaper från båda. Båda involverar beräkningar i hög skala, oftast i datorkluster. Men lösningens metoder och stödverktyg kan skilja sig åt.

• **Big Compute** omfattar ofta program som förlitar sig på processorkraft och minne, till exempel tekniska simuleringar, finansiell riskmodellering och digital rendering. Infrastrukturen för en Big Compute-lösning kan innehålla datorer med särskilda flerkärniga processorer som utför rådataberäkningar, och särskild maskinvara för höghastighetsnätverk som ansluter datorerna.

• **Stordata** löser dataanalysproblem som omfattar stora mängder data som inte kan hanteras av en enskild dator eller ett enskilt databashanteringssystem. Bland exemplen finns stora mängder webbloggar eller andra BI-data (Business Intelligence). Användningen av stordata är mer beroende av diskkapacitet och I/O-prestanda än av processorkraft. Det finns speciella stordataverktyg, till exempel Apache Hadoop, som du kan använda till att hantera kluster och partitionera data. (Information om Azure HDInsight och andra Azure Hadoop-lösningar finns i [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Beräkningshantering och schemaläggning av jobb
När Batch- och HPC-program körs används ofta en *klusterhanterare* och ett *jobbschema* för att hantera klustrade beräkningsresurser och allokera dem till programmen som kör jobben. Dessa funktioner kan utföras av olika verktyg, eller ett integrerat verktyg eller en integrerad tjänst.

* **Klusterhanterare** – Etablerar, frisläpper och administrerar beräkningsresurser (eller beräkningsnoder). En klusterhanterare kan automatisera installationen av operativsystemavbildningar och program på datornoder, skala beräkningsresurser efter behov och övervaka nodernas prestanda.
* **Jobbschema** – Anger vilka resurser (t.ex. processorer eller minne) som ett program behöver och villkoren då det körs. En jobbschemat har en kö med jobb och tilldelar resurser till dem utifrån en tilldelad prioritet eller andra egenskaper.

Kluster- och schemaläggningsverktyg för Windows- och Linux-baserade kluster kan migreras till Azure. Exempelvis har [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), Microsofts kostnadsfria beräkningsklusterlösning för Windows- och Linux HPC-arbetsbelastningar, flera alternativ för körning i Azure. Du kan också skapa Linux-kluster för att köra verktyg baserade på öppen källkod, som till exempel Torque och SLURM. Du kan också hämta kommersiella lösningar till Azure, exempelvis [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), [IBM Spectrum Symphony och Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/) och [Univa Grid Engine](http://www.univa.com/products/grid-engine).

Som du ser i följande avsnitt kan du också dra nytta av Azure-tjänster för att hantera beräkningsresurser och schemalägga jobb utan (eller förutom) traditionella klusterhanteringsverktyg.

## <a name="scenarios"></a>Scenarier
Här är tre vanliga scenarier som illustrerar hur du kan köra Big Compute-arbetsbelastningar i Azure genom att utnyttja befintliga HCP-klusterlösningar, Azure-tjänster eller en kombination av båda. Viktiga saker att tänka på när du väljer respektive scenario framhävs, men listan är inte fullständig. Mer information om de tillgängliga Azure-tjänster som du kan använda i din lösning finns längre fram i artikeln.

| Scenario | Varför ska jag välja det? |
| --- | --- | --- |
| **Burst- och HPC-kluster till Azure**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Läs mer:<br/>• [Burst to Azure worker-instanser med HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Konfigurera ett hybridberäkningskluster med HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Burst-överför till Azure Batch med HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• Kombinera [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) eller andra lokala kluster med ytterligare Azure-resurser i en hybridlösning.<br/><br/>• Utöka dina Big Compute-arbetsbelastningar att köra på instanser av virtuella PaaS-datorer (Platform as a Service) (för närvarande endast Windows Server).<br/><br/>• Kom åt en lokal licensserver eller databas genom att använda ett valfritt virtuellt Azure-nätverk. |
| **Skapa ett HPC-kluster helt och hållet i Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Läs mer:<br/>• [HPC-klusterlösningar i Azure](big-compute-resources.md)<br/><br/> |• Distribuera snabbt och konsekvent program och klusterverktyg på standardiserade eller anpassade Windows- eller Linux-baserade virtuella IaaS-datorer (Infrastructure as a Service).<br/><br/>• Kör flera olika Big Compute-arbetsbelastningar med hjälp av valfri jobbschemalösning.<br/><br/>• Använd ytterligare Azure tjänster, inklusive nätverk och lagring för att skapa fullständiga molnbaserade lösningar. |
| **Skala ut ett parallellt program till Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Läs mer:<br/>• [Grunderna i Azure Batch](batch-technical-overview.md)<br/><br/>• [Komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md) |• Utveckla med [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) och skala ut en rad olika Big Compute-arbetsbelastningar för körning i pooler på virtuella Windows eller Linux-datorer.<br/><br/>• Använd en Azure-plattform för att hantera distributionen och den automatiska skalningen av virtuella datorer, schemaläggning av jobb, haveriberedskap, dataflyttning, beroendehantering och programdistribution. |

## <a name="azure-services-for-big-compute"></a>Azure-tjänster för Big Compute
Här finns mer information om beräkningar, data, nätverk och relaterade tjänster som du kan kombinera för lösningar och arbetsflöden med Big Compute. Detaljerad information om Azure-tjänster finns i [dokumentationen](https://azure.microsoft.com/documentation/) för Azure-tjänsterna. [Scenarier](#scenarios) tidigare i den här artikeln visar bara några sätt som du kan använda dessa tjänster på.

> [!NOTE]
> Azure introducerar regelbundet nya tjänster som kan vara användbara för ditt scenario. Om du har frågor kan du kontakta en [Azure-partner](https://pinpoint.microsoft.com/en-US/search?keyword=azure) eller skicka ett e-postmeddelande till *bigcompute@microsoft.com*.
> 
> 

### <a name="compute-services"></a>Beräkningstjänster
Azures beräkningstjänster är kärnan i en Big Compute-lösning och olika beräkningstjänster har olika fördelar för olika scenarier. På en grundläggande nivå erbjuder dessa tjänster olika lägen som program kan köras i på VM-baserade beräkningsinstanser som Azure tillhandahåller med hjälp av Windows Server Hyper-V-teknik. Dessa instanser kan köra standardiserade eller anpassade Linux- och Windows-baserade operativsystem och verktyg. Azure ger dig möjlighet att välja [instansstorlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) med olika konfigurationer av CPU-kärnor, minne, diskutrymme och andra egenskaper. Beroende på dina behov kan du skala instanser till flera tusen kärnor och sedan skala ned när du behöver färre resurser.

> [!NOTE]
> Dra nytta av Azures [beräkningsintensiva instanser, till exempel H-serien](../virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att förbättra prestanda och skalbarhet för HPC-arbetsbelastningar. Dessa instanser stöder även parallella MPI-program som kräver ett nätverk med låg latens och hög genomströmning. Även virtuella datorer med NVIDIA-grafikprocessorer i [N-serien](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/) är tillgängliga för att utöka utbudet av databehandlings- och visualiseringsscenarier i Azure.  
> 
> 

| Tjänst | Beskrivning |
| --- | --- |
| **[Virtuella datorer](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Tillhandahåller beräkning i en IaaS-modell (Infrastructure as a Service) med hjälp av Microsoft Hyper-V-teknik.<br/><br/>• Gör att du kan etablera och hantera beständiga molndatorer från Windows Server- eller Linux-standardavbildningar från [Azure Marketplace](https://azure.microsoft.com/marketplace/), eller avbildningar och datadiskar som du tillhandahåller<br/><br/>• Kan distribueras och hanteras som [VM-skalningsuppsättningar](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) för att skapa storskaliga tjänster från identiska virtuella datorer, med automatisk skalning som ökar eller minskar kapaciteten automatiskt.<br/><br/>• Kör verktyg och program för lokala beräkningskluster helt i molnet.<br/><br/> |
| **[Molntjänster](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Kan köra Big Compute-program i arbetarrollinstanser, som är virtuella datorer som kör en version av Windows Server och som hanteras helt av Azure.<br/><br/>• Ger möjlighet att använda skalbara och tillförlitliga program med mycket lite administrativt arbete som körs i en PaaS-modell (Platform as a Service).<br/><br/>• Kan kräva ytterligare verktyg eller utveckling för att integrera med lokala HPC-klusterlösningar. |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Kör storskaliga parallella arbetsbelastningar och batch-arbetsbelastningar i en helt hanterad tjänst.<br/><br/>• Tillhandahåller schemaläggning av jobb och automatisk skalning av en hanterad pool med virtuella datorer.<br/><br/>• Gör att utvecklare kan skapa och köra program som en tjänst eller aktivera befintliga program för molnet.<br/> |

### <a name="storage-services"></a>Lagringstjänster
En Big Compute-lösning fungerar vanligtvis med en uppsättning indata och genererar data som resultat. Här är exempel på några Azure Storage-tjänster som används i Big Compute-lösningar:

* [Blob Storage, Table Storage och Queue Storage](https://azure.microsoft.com/documentation/services/storage/) – Hantera stora mängder ostrukturerade data, NoSQL-data och meddelanden för arbetsflöden respektive kommunikation. Du kan exempelvis använda Blob Storage för stora mängder tekniska data eller för de indatabilder eller mediefiler som ditt program bearbetar. Du kan använda köer för asynkron kommunikation i en lösning. Mer information finns i [Introduktion till Microsoft Azure Storage](../storage/storage-introduction.md).
* [Azure File Storage](https://azure.microsoft.com/services/storage/files/) – Delar vanliga filer och data i Azure med SMB-standardprotokollet, som krävs för vissa HPC-klusterlösningar.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) – Tillhandahåller storskaligt Apache Hadoop Distributed File System för molnet, som är särskilt användbart för batch- och realtidsanalyser samt interaktiva analyser.

### <a name="data-and-analysis-services"></a>Data- och analystjänster
Vissa Big Compute-scenarier omfattar storskaliga dataflöden eller genererar data som behöver ytterligare bearbetning eller analys. Azure erbjuder flera olika data- och analystjänster, inklusive:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) – Bygger datadrivna arbetsflöden (pipelines) som ansluter, aggregerar och transformerar data från lokala, molnbaserade och Internetbaserade datalager.
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) – Tillhandahåller huvudfunktionerna i ett Microsoft SQL Server-baserat  relationsdatabashanteringssystem i en hanterad tjänst.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) – Distribuerar och etablerar Windows Server- eller Linux-baserade Apache Hadoop-kluster i molnet för att hantera, analysera och rapportera om stordata.
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) – Hjälper dig att skapa, testa, använda och hantera lösningar för förutsägbar analys i en helt hanterad tjänst.

### <a name="additional-services"></a>Ytterligare tjänster
Din Big Compute-lösning kan behöva andra Azure-tjänster för att ansluta till resurser lokalt eller i andra miljöer. Exempel:

* [Virtuellt nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) – Skapar ett logiskt, isolerat avsnitt i Azure för att koppla ihop Azure-resurser med varandra eller med ditt lokala datacenter. Med ett virtuellt nätverk som sträcker sig över flera platser kan Big Compute-program komma åt lokala data, Active Directory-tjänster och licensservrar
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) – Skapar privata anslutningar mellan Microsofts datacenter och infrastruktur som finns lokalt eller i en samplaceringsmiljö. ExpressRoute ger högre säkerhetsnivåer, bättre tillförlitlighet, snabbare hastigheter och kortare svarstider än vanliga anslutningar över Internet.
* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) – Tillhandahåller flera mekanismer som gör att program kan kommunicera eller utbyta data, oavsett om de finns i Azure, på en annan molnplattform eller i ett datacenter.

## <a name="next-steps"></a>Nästa steg
* Teknisk hjälp med att skapa en egen lösning finns i [Tekniska resurser för Batch och HPC](big-compute-resources.md).
* Diskutera dina Azure-alternativ med partner, inklusive Cycle Computing, Rescale och UberCloud.
* Läs mer om Big Compute-lösningar i Azure från [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) och [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).
* Du hittar den senaste informationen i [Microsoft HPC- och Batch-bloggen](http://blogs.technet.com/b/windowshpc/) och i [Azure-bloggen](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

