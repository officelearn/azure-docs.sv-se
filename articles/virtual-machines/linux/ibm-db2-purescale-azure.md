---
title: IBM DB2 pureScale på Azure
description: I den här artikeln visar vi en arkitektur för att köra en IBM DB2 pureScale miljö på Azure.
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
ms.openlocfilehash: 901afc8f28b617eb5bada2a0f58761ddb9f67607
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327423"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale på Azure

IBM DB2 pureScale miljön innehåller ett databas-kluster för Azure med hög tillgänglighet och skalbarhet på Linux-operativsystem. Den här artikeln visar en arkitektur för att köra DB2 pureScale på Azure.

## <a name="overview"></a>Översikt

Företag har länge använt relationsdatabas hanteringsplattformar system (RDBMS) för sina online transaktionsbearbetning (OLTP) behov. Dessa dagar migrerar många sina miljöer stordatorprogram-baserad databas till Azure som ett sätt att utöka kapaciteten, minska kostnaderna och upprätthålla en stadig driftskostnaderna struktur.

Migreringen är ofta det första steget i att modernisera ett äldre plattform. Till exempel rehosted en företagskund nyligen dess IBM DB2-miljö som körs på z/OS till IBM DB2 pureScale på Azure. Även om det är inte identiska med den ursprungliga miljön, ger IBM DB2 pureScale på Linux liknande hög tillgänglighet och skalbarhetsfunktioner som IBM DB2 för z/OS som körs i en parallell Sysplex konfiguration på stordatorprogram.

Den här artikeln beskriver den arkitektur som används för den här Azure-migrering. Kunden har använt Red Hat Linux 7.4 för att testa konfigurationen. Den här versionen är tillgänglig från Azure Marketplace. Innan du väljer en Linux-distribution kan du se till att kontrollera versionerna som stöds för närvarande. Mer information finns i dokumentationen för [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) och [GlusterFS](https://docs.gluster.org/en/latest/).

Den här artikeln är en startpunkt för dina DB2 implementeringsplan. Företagets krav varierar, men samma grundläggande mönster gäller. Du kan också använda den här arkitektoniskt mönster för online analytical processing (OLAP)-program på Azure.

Den här artikeln täcker inte skillnader och möjliga migreringen för att flytta en IBM DB2 för z/OS-databasen till IBM DB2 pureScale som körs på Linux. Och det tillhandahåller inte uppskattningar av storlek och arbetsbelastning analyser för att flytta från DB2 z/OS till DB2 pureScale. 

För att hjälpa dig att välja den bästa DB2 pureScale arkitekturen för din miljö, rekommenderar vi du fullständigt beräkna storlek och gör en hypotes. Se till att tänka på DB2 z/OS parallellt i källsystemet, Sysplex med arkitektur för delning av data, koppling anläggning konfiguration och användningsstatistik för distribuerade data anläggning (DDF).

> [!NOTE]
> Den här artikeln beskriver en metod för migrering av DB2, men det finns andra. Till exempel kan DB2 pureScale också köra i virtualiserade lokala miljöer. IBM stöder DB2 på Microsoft Hyper-V i olika konfigurationer. Mer information finns i [virtualiseringsarkitektur för DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) i IBM Knowledge Center.

## <a name="architecture"></a>Arkitektur

För hög tillgänglighet och skalbarhet i Azure måste använda du en skalbar, delade data-arkitektur för DB2 pureScale. Kund-migreringen används följande exempel arkitektur.

![DB2 pureScale på Azure virtual machines som visar lagring och nätverk](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale på Azure virtual machines som visar lagring och nätverk")


Diagrammet visar logiska lager som behövs för ett DB2 pureScale kluster. Dessa inkluderar virtuella datorer för en klient, för hantering, för cachelagring, för database engine och för delad lagring. 

Förutom motorn databasnoderna innehåller diagrammet två noder som används för klustret cachelagring anläggningar (CFs). Minst två noder används för databasmotorn själva. En DB2-server som hör till ett pureScale kluster kallas för en medlem. 

Klustret är ansluten via iSCSI-en GlusterFS delad lagringskluster med tre noder att tillhandahålla lagring för skalbarhet och hög tillgänglighet. DB2 pureScale installeras på virtuella Azure-datorer som kör Linux.

Den här metoden är en mall som du kan ändra storlek och skala för din organisation. Den baseras på följande:

-   Två eller flera medlemmar i databasen kombineras med minst två CF noder. Noderna hantera globala buffertpoolen (sek) för delat minne och globala Lås manager (GLM)-tjänster för att styra delad åtkomst och på låsskärmen konkurrens från aktiva medlemmar. En nod för CF fungerar som primärt och den andra som sekundär redundans CF noden. För att undvika en enskild felpunkt i miljön kan kräver ett DB2 pureScale kluster minst fyra noder.

-   Delad lagring med höga prestanda (visas i P30-storlek i diagrammet). Varje nod Gluster FS använder den här lagringen.

-   Högpresterande nätverk för datamedlemmar och delad lagring.

### <a name="compute-considerations"></a>Compute-överväganden

Den här arkitekturen körs nivåerna program, lagring och data på virtuella Azure-datorer. Den [distribution installationsskripten](http://aka.ms/db2onazure) skapa följande:

-   Ett DB2 pureScale kluster. Vilken typ av beräkningsresurser som du behöver på Azure beror på din konfiguration. I allmänhet kan du använda två sätt:

    -   Använda en flera noder, högpresterande datorbearbetning (HPC)-style nätverk där små till medelstora instanser komma åt delad lagring. För den här HPC-typen av konfiguration Azure minnesoptimerade E-serien eller lagringsoptimerade L-serien [virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) ger databearbetningskraft som krävs.

    -   Använda färre stor VM-instanser för datamotorer. För stora instanser, det största minnesoptimerade [M-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuella datorer är idealiska för tunga minnesinterna arbetsbelastningar. Du kanske behöver en dedikerad instans, beroende på storleken på den logiska partition (LPAR) som används för att köra DB2.

-   DB2-CF använder sig av minnesoptimerade virtuella datorer, till exempel E- eller L-serien.

-   GlusterFS storage använder Standard\_DS4\_v2-datorer som kör Linux.

-   En GlusterFS jumpbox är en Standard\_DS2\_v2-dator som kör Linux.

-   Klienten är en Standard\_DS3\_v2-dator som kör Windows (används för testning).

-   Ett vittnesserver är en Standard\_DS3\_v2-dator som kör Linux (används för DB2 pureScale).

> [!NOTE]
> Ett DB2 pureScale kluster kräver minst två DB2-instanser. Det kräver också en cache-instans och en Lås manager-instans.

### <a name="storage-considerations"></a>Överväganden för lagring

T.ex. Oracle RAC är DB2 pureScale en högpresterande block i/o, skalbar databas. Vi rekommenderar att du använder den största [Azure premium SSD](disks-types.md) alternativ som passar dina behov. Mindre lagringsalternativ kan vara lämplig för utvecklings- och testmiljöer produktionsmiljöer behöver ofta mer lagringskapacitet. Exempel-arkitekturen används [P30](https://azure.microsoft.com/pricing/details/managed-disks/) på grund av dess förhållandet mellan IOPS och storlek och pris. Oavsett storlek, använda Premium Storage för bästa prestanda.

DB2 pureScale använder en delad – allt arkitektur, där alla data kan nås från alla noder i klustret. Premium-lagring måste delas mellan instanser, oavsett om du är på begäran eller enligt dedikerade instanser.

Ett stort DB2 pureScale kluster kan kräva 200 terabyte (TB) eller flera av premium delad lagring, med 100 000 IOPS. DB2 pureScale stöder ett block iSCSI-gränssnitt som du kan använda i Azure. ISCSI-gränssnitt kräver ett kluster med delad lagring som du kan implementera med GlusterFS, S2D eller något annat verktyg. Den här typen av lösning skapar en enhet för virtuella storage area network (vSAN) i Azure. DB2 pureScale använder det virtuella SAN-nätverket för att installera det klustrade filsystem som används för att dela data mellan virtuella datorer.

Exempel-arkitekturen används GlusterFS, ett kostnadsfritt, skalbart, open source-distribuerat filsystem som är optimerad för molnlagring.

### <a name="networking-considerations"></a>Nätverksöverväganden

IBM rekommenderar InfiniBand-nätverk för alla medlemmar i en DB2 pureScale kluster. DB2 pureScale använder också direktåtkomst till fjärrminne (RDMA), om det finns för CFs.

Under installationen kan du skapa en Azure [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) som innehåller alla virtuella datorer. I allmänhet kan du gruppera resurser baserat på deras livstid och vem kommer att hantera. De virtuella datorerna i den här arkitekturen kräver [accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Det är en funktion i Azure som ger konsekvent och extremt låg nätverksfördröjning via enskild rot i/o-virtualisering (SR-IOV) till en virtuell dator.

Varje Azure-dator har distribuerats till ett virtuellt nätverk med undernät: main, Gluster FS klientdelen (gfsfe), Gluster FS serverdel (bfsbe), DB2 pureScale (db2be) och DB2 pureScale front sluta (db2fe). Installationsskriptet skapar även primärt [nätverkskort](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) på de virtuella datorerna i det huvudsakliga undernätet.

Använd [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) att begränsa nätverkstrafiken inom det virtuella nätverket och isolera undernät.

På Azure måste DB2 pureScale använda TCP/IP som nätverksanslutningen för lagring.

## <a name="next-steps"></a>Nästa steg

-   [Distribuera den här arkitekturen i Azure](deploy-ibm-db2-purescale-azure.md)
