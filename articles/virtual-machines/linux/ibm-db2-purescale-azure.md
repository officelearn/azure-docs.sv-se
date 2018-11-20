---
title: IBM Db2 pureScale på Azure
description: I den här artikeln visar vi en arkitektur för att köra en IBM Db2 pureScale miljö på Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977728"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM Db2 pureScale på Azure

IBM Db2 pureScale miljö finns en lösning för databasen för Azure med hög tillgänglighet och skalbarhet på Linux-operativsystem. I den här artikeln visar vi en arkitektur för att köra Db2 pureScale på Azure.

## <a name="overview"></a>Översikt

Företag har länge använt traditionella relationsdatabaser hanteringsplattformar system (RDBMS) för att serva online transaktionsbearbetning (OLTP) behov. Dessa dagar migrerar många sina miljöer stordatorprogram-baserad databas till Azure som ett sätt att utöka kapaciteten, minska kostnaderna och upprätthålla en stadig driftskostnaderna struktur.

Migreringen är ofta det första steget i att modernisera ett äldre plattformen. Till exempel rehosted en enterprise nyligen sin IBM Db2-miljö som körs på z/OS till IBM Db2 pureScale på Azure. Även om inte identisk med den ursprungliga miljön innehåller IBM Db2 pureScale på Linux liknande hög tillgänglighet och skalbarhet funktioner som IBM Db2 för z/OS som körs i en parallell Sysplex konfiguration på stordatorprogram.

Den här artikeln beskriver den arkitektur som används för den här Azure-migrering. Red Hat Linux 7.4 har använts för att testa konfigurationen. Den här versionen är tillgänglig från Azure Marketplace. Innan du väljer en Linux-distribution måste du se till att kontrollera versionerna som stöds för närvarande. Mer information finns i dokumentationen för [IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) och [GlusterFS](https://docs.gluster.org/en/latest/).

Den här artikeln är bara en startpunkt för dina Db2 implementeringsplan. Företagets krav varierar, men samma grundläggande mönster gäller. Den här arkitektoniskt mönster kan också användas för online analytical processing (OLAP)-program på Azure.

Den här artikeln beskriver inte skillnader och möjliga migreringen för att flytta en IBM Db2 för z/OS-databasen till IBM Db2 pureScale som körs på Linux. Inte heller kan det ge uppskattningar på motsvarande storlek och arbetsbelastning analyser för att flytta från Db2 z/OS till Db2 pureScale. Att hjälpa dig att välja den bästa Db2 pureScale arkitekturen för din miljö, rekommenderar vi starkt att du har slutfört en fullständig storlek uppskattning utöva och upprätta en hypotes. Bland andra faktorer på källsystemet kontrollerar du att tänka på Db2 z/OS parallella Sysplex med arkitekturen för delning av Data, koppling anläggning konfiguration och DDF användningsstatistik.

> [!NOTE]
> Den här artikeln beskriver en metod för migrering av Db2, men det finns andra. Till exempel kan Db2 pureScale också köra i virtualiserade lokala miljöer. IBM stöder Db2 på Microsoft Hyper-V i olika konfigurationer. Mer information finns i [virtualiseringsarkitektur för Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) i IBM Knowledge Center.

## <a name="architecture"></a>Arkitektur

Stöd för hög tillgänglighet och skalbarhet på Azure, en skalbar kan arkitektur för delade data användas för Db2 pureScale. Följande exempel arkitektur har använts för våra kunder-migrering.

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "Db2 pureScale på Azure virtual machines som visar lagring och nätverk")


Arkitektoniska diagram visar de logiska lager som behövs för ett kluster för Db2 pureScale. Dessa inkluderar virtuella datorer för en klient, för hantering, för cachelagring, för database engine och för delad lagring. Förutom motorn databasnoderna innehåller också två noder som används för vad kallas klustret cachelagring anläggningar (CFs) i diagrammet. Minst två noder som används för database engine. en Db2-server som hör till ett pureScale kluster kallas för en medlem. Klustret är ansluten via iSCSI-en GlusterFS delad lagringskluster med tre noder att tillhandahålla lagring för skalbarhet och hög tillgänglighet. Db2 pureScale installeras på virtuella Azure-datorer som kör Linux.

Den här metoden är helt enkelt en mall som du kan ändra så att den passar storleken och skala som behövs i organisationen och baseras på följande:

-   Två eller flera medlemmar i databasen kombineras med minst två CF noderna, vilket hanterar globala buffertpoolen (sek) för delat minne och globala Lås manager (GLM)-tjänster för att styra delad åtkomst och på låsskärmen konkurrens från flera aktiva medlemmar. En nod för CF fungerar som primärt och den andra som sekundär redundans CF noden. Om du vill undvika att skapa en miljö som har en enskild felpunkt, krävs minst fyra noder i ett kluster med Db2 pureScale.

-   Delad lagring med höga prestanda (visas i P30-storlek i bild 1), som används av varje Gluster FS-nod.

-   Högpresterande nätverk för datamedlemmar och delad lagring.

### <a name="compute-considerations"></a>Compute-överväganden

Den här arkitekturen körs nivåerna program, lagring och data på virtuella Azure-datorer. Den [distribution installationsskripten](http://aka.ms/db2onazure) skapa följande:

-   Ett Db2 pureScale kluster. Typ av beräkningsresurser som du behöver på Azure beror på din konfiguration. I allmänhet finns det två sätt som du kan använda:

    -   Använda en flera noder, högpresterande datorbearbetning (HPC)-style nätverk där flera små till medelstora instanser komma åt delad lagring. För den här HPC-typen av konfiguration Azure minnesoptimerade E-serien eller lagringsoptimerade L-serien [virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) ger beräkningarna power som behövs.

    -   Använda färre stor VM-instanser för datamotorer. För stora instanser av de största minnesoptimerade [M-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuella datorer är idealiska för tunga minnesinterna arbetsbelastningar, men en dedikerad instans kan krävas, beroende på storleken på den logiska Partition (LPAR) som används för att köra Db2.

-   Db2-CF använder sig av minnesoptimerade virtuella datorer, till exempel E- eller L-serien.

-   GlusterFS storage använder Standard\_DS4\_v2-datorer som kör Linux.

-   En GlusterFS jumpbox är en Standard\_DS2\_v2-dator som kör Linux.

-   Klienten är en Standard\_DS3\_v2-dator som kör Windows (används för testning).

-   Ett vittnesserver är en Standard\_DS3\_v2-dator som kör Linux (används för Db2 pureScale).

> [!NOTE]
> Minst två Db2-instanser krävs i en Db2 pureScale kluster. En Cache-instans och Låshanteraren instans måste också anges.

### <a name="storage-considerations"></a>Överväganden för lagring

T.ex. Oracle RAC är Db2 pureScale en högpresterande block i/o, skalbar databas. Vi rekommenderar att du använder den största [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) tillgängliga som passar dina behov. Mindre lagringsalternativ kan till exempel vara lämplig för utvecklings- och testmiljöer när produktionsmiljöer kräver ofta större lagringskapacitet. Exempel-arkitekturen används [P30](https://azure.microsoft.com/pricing/details/managed-disks/) på grund av dess förhållandet mellan IOPS och storlek och pris. Oavsett storlek, använda Premium Storage för bästa prestanda.

Db2 pureScale använder en delad allt arkitektur, där alla data kan nås från alla noder i klustret. Premium-lagring måste delas över flera instanser, oavsett om det på begäran eller på dedikerade instanser.

Ett stort Db2 pureScale kluster kan kräva 200 terabyte (TB) eller högre i Premium delat lagringsutrymme med 100 000 IOPS. Db2 pureScale har stöd för ett gränssnitt för iSCSI-block som kan användas på Azure. ISCSI-gränssnitt kräver ett kluster med delad lagring som kan implementeras med GlusterFS, S2D eller något annat verktyg. Den här typen av lösning skapar en enhet för virtuella storage area network (vSAN) i Azure. Db2 pureScale använder det virtuella SAN-nätverket för att installera det klustrade filsystem som används för att dela data mellan flera virtuella datorer.

Vi använde GlusterFS filsystem, ett filsystem för kostnadsfria, skalbara och öppen källkod som optimerats för molnlagring för den här arkitekturen.

### <a name="networking-considerations"></a>Nätverksöverväganden

IBM rekommenderar InfiniBand-nätverk för alla medlemmar i ett kluster för Db2 pureScale; Db2 pureScale använder också direktåtkomst till fjärrminne (RDMA), om det finns för CFs används.

Under installationen, en Azure [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) skapas för att innehålla alla virtuella datorer. I allmänhet är grupperade baserat på deras livstid och vem kommer att hantera. De virtuella datorerna i den här arkitekturen kräver [nätverksaccelerering](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), en funktion i Azure som ger konsekvent och extremt låg nätverksfördröjning via i/o-virtualisering (SR-IOV) till en virtuell dator.

Alla Azure virtuella datorer distribueras till ett virtuellt nätverk som delas upp i flera undernät: main, Gluster FS klientdelen (gfsfe), Gluster FS serverdel (bfsbe), Db2 pureScale (db2be) och Db2 purescale front sluta (db2fe). Installationsskriptet skapar även primärt [nätverkskort](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) på de virtuella datorerna i det huvudsakliga undernätet.

[Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) används för att begränsa nätverkstrafiken inom det virtuella nätverket och isolera undernät.

På Azure måste Db2 pureScale använda TCP/IP som nätverksanslutningen för lagring.

## <a name="next-steps"></a>Nästa steg

-   [Distribuera den här arkitekturen i Azure](deploy-ibm-db2-purescale-azure.md)
