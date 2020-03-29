---
title: IBM DB2 pureScale på Azure
description: I den här artikeln visar vi en arkitektur för att köra en IBM DB2 pureScale-miljö på Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945062"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale på Azure

IBM DB2 pureScale-miljön tillhandahåller ett databaskluster för Azure med hög tillgänglighet och skalbarhet på Linux-operativsystem. Den här artikeln visar en arkitektur för att köra DB2 pureScale på Azure.

## <a name="overview"></a>Översikt

Företag har länge använt traditionella relationsdatabashanteringssystem (RDBMS) plattformar för att tillgodose deras online transaktionsbearbetning (OLTP) behov. Nuförtiden migrerar många sina stordatorbaserade databasmiljöer till Azure som ett sätt att utöka kapaciteten, minska kostnaderna och upprätthålla en stadig driftkostnadsstruktur. Migration är ofta det första steget för att modernisera en äldre plattform. 

Nyligen, ett företag kund rehosted sin IBM DB2 miljö som körs på z / OS till IBM DB2 pureScale på Azure. Db2 pureScale-databasklusterlösningen ger hög tillgänglighet och skalbarhet på Linux-operativsystem. Kunden körde Db2 framgångsrikt som en fristående, skala upp instans på en enda virtuell dator (VM) i ett storskaligt system på Azure innan du installerar Db2 pureScale. 

Även om inte identisk med den ursprungliga miljön, IBM DB2 pureScale på Linux ger liknande hög tillgänglighet och skalbarhet funktioner som IBM DB2 för z / OS som körs i en parallell Sysplex konfiguration på stordatorn. I det här fallet är klustret anslutet via iSCSI till ett delat lagringskluster. Vi använde GlusterFS filsystem, en gratis, skalbar, öppen källkod distribuerade filsystem speciellt optimerad för molnlagring. IBM stöder dock inte längre den här lösningen. För att behålla ditt stöd från IBM måste du använda ett iSCSI-kompatibelt filsystem som stöds. Microsoft erbjuder Storage Spaces Direct (S2D) som tillval

I den här artikeln beskrivs den arkitektur som används för den här Azure-migreringen. Kunden använde Red Hat Linux 7.4 för att testa konfigurationen. Den här versionen är tillgänglig från Azure Marketplace. Innan du väljer en Linux-distribution måste du kontrollera de versioner som stöds. Mer information finns i dokumentationen för [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) och [GlusterFS](https://docs.gluster.org/en/latest/).

Den här artikeln är en utgångspunkt för implementeringsplanen för DB2. Dina affärskrav kommer att skilja sig åt, men samma grundläggande mönster gäller. Du kan också använda det här arkitektoniska mönstret för OLAP-program (Online Analytical Processing) på Azure.

Den här artikeln täcker inte skillnader och möjliga migreringsuppgifter för att flytta en IBM DB2 för z/OS-databas till IBM DB2 pureScale som körs på Linux. Och det ger inte storleksuppskattningar och arbetsbelastningsanalyser för att flytta från DB2 z/OS till DB2 pureScale. 

För att hjälpa dig att bestämma den bästa DB2 pureScale-arkitekturen för din miljö rekommenderar vi att du uppskattar storleksändringen och gör en hypotes. På källsystemet, se till att överväga DB2 z/OS Parallel Sysplex med datadelning arkitektur, kopplingsanordning konfiguration och distribuerade data facility (DDF) användningsstatistik.

> [!NOTE]
> Den här artikeln beskriver en metod för DB2-migrering, men det finns andra. DB2 pureScale kan till exempel även köras i virtualiserade lokala miljöer. IBM stöder DB2 på Microsoft Hyper-V i olika konfigurationer. Mer information finns i [DB2 pureScale-virtualiseringsarkitektur](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) i IBM Knowledge Center.

## <a name="architecture"></a>Arkitektur

För att stödja hög tillgänglighet och skalbarhet på Azure kan du använda en utskalning, delad dataarkitektur för DB2 pureScale. Kundmigreringen använde följande exempelarkitektur.

![DB2 pureScale på virtuella Azure-datorer som visar lagring och nätverk](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale på virtuella Azure-datorer som visar lagring och nätverk")


Diagrammet visar de logiska lager som behövs för ett DB2 pureScale-kluster. Dessa inkluderar virtuella datorer för en klient, för hantering, cachelagring, databasmotor och för delad lagring. 

Förutom databasmotornoderna innehåller diagrammet två noder som används för klustercif-enheter (CFs). Minst två noder används för själva databasmotorn. En DB2-server som tillhör ett pureScale-kluster kallas medlem. 

Klustret är anslutet via iSCSI till ett delat lagringskluster med tre noder för att tillhandahålla utskalningslagring och hög tillgänglighet. DB2 pureScale installeras på virtuella Azure-datorer som kör Linux.

Den här metoden är en mall som du kan ändra för organisationens storlek och skala. Den är baserad på följande:

-   Två eller flera databasmedlemmar kombineras med minst två CF-noder. Noderna hanterar en global buffertpool (GBP) för delade minnes- och globala låshanterare (GLM) tjänster för att styra delad åtkomst och låsa konkurrens från aktiva medlemmar. En CF-nod fungerar som den primära och den andra som den sekundära, redundans CF-noden. För att undvika en enda felpunkt i miljön kräver ett DB2 pureScale-kluster minst fyra noder.

-   Högpresterande delad lagring (visas i P30-storlek i diagrammet). Varje nod använder den här lagringen.

-   Högpresterande nätverk för datamedlemmar och delad lagring.

### <a name="compute-considerations"></a>Beräkningsöverväganden

Den här arkitekturen kör program-, lagrings- och datanivåer på virtuella Azure-datorer. [Distributionskonfigurationsskripten](https://aka.ms/db2onazure) skapar följande:

-   En DB2 pureScale kluster. Vilken typ av beräkningsresurser du behöver på Azure beror på din konfiguration. I allmänhet kan du använda två metoder:

    -   Använd ett HPC-nätverk (High-Node, High-Performance Computing) där små och medelstora instanser får åtkomst till delad lagring. För den här HPC-typen av konfiguration tillhandahåller Azure-minnesoptimerade [virtuella](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) E-serie- eller lagringsoptimerade virtuella datorer i L-serien den beräkningskraft som behövs.

    -   Använd färre stora instanser av virtuella datorer för datamotorerna. För stora instanser är de största minnesoptimerade virtuella datorerna i [M-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/) idealiska för tunga arbetsbelastningar i minnet. Du kan behöva en dedikerad instans, beroende på storleken på den logiska partitionen (LPAR) som används för att köra DB2.

-   DB2 CF använder minnesoptimerade virtuella datorer, till exempel E-serien eller L-serien.

-   Ett delat lagringskluster som\_använder virtuella standardDS4\_v2-datorer som kör Linux.

-   Hanteringshoppboxen är\_en\_virtuell standard DS2 v2 virtuell dator som kör Linux.  Ett alternativ är Azure Bastion, en tjänst som ger en säker RDP/SSH-upplevelse för alla virtuella datorer i ditt virtuella nätverk.

-   Klienten är\_en virtuell\_standard DS3 v2-dator som kör Windows (används för testning).

-   *Valfritt*. En vittnesserver. Detta behövs endast med vissa tidigare versioner av Db2 pureScale. I det här\_exemplet\_används en virtuell standardDS3 v2 virtuell dator som kör Linux (används för DB2 pureScale).

> [!NOTE]
> En DB2 pureScale kluster kräver minst två DB2 instanser. Det kräver också en cache-instans och en låshanterare instans.

### <a name="storage-considerations"></a>Överväganden för lagring

Liksom Oracle RAC är DB2 pureScale en högpresterande block I/O, skalningsdatabas. Vi rekommenderar att du använder det största [Azure premium SSD-alternativet](disks-types.md) som passar dina behov. Mindre lagringsalternativ kan vara lämpliga för utvecklings- och testmiljöer, medan produktionsmiljöer ofta behöver mer lagringskapacitet. Exempelarkitekturen använder [P30](https://azure.microsoft.com/pricing/details/managed-disks/) på grund av dess förhållande mellan IOPS och storlek och pris. Oavsett storlek, använd Premium Storage för bästa prestanda.

DB2 pureScale använder en arkitektur med delat allt, där alla data är tillgängliga från alla klusternoder. Premiumlagring måste delas mellan flera instanser, oavsett om det finns på begäran eller på dedikerade instanser.

Ett stort DB2 pureScale-kluster kan kräva 200 terabyte (TB) eller mer av delad premiumlagring, med IOPS på 100 000. DB2 pureScale stöder ett iSCSI-blockgränssnitt som du kan använda på Azure. iSCSI-gränssnittet kräver ett delat lagringskluster som du kan implementera med S2D eller ett annat verktyg. Den här typen av lösning skapar en virtuell lagringsnätverksenhet (vSAN) i Azure. DB2 pureScale använder vSAN för att installera det klustrade filsystem som används för att dela data mellan virtuella datorer.

### <a name="networking-considerations"></a>Nätverksöverväganden

IBM rekommenderar InfiniBand-nätverk för alla medlemmar i ett DB2 pureScale-kluster. DB2 pureScale använder också fjärråtkomst till direkt minne (RDMA), om tillgängligt, för CFs.

Under installationen skapar du en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) som innehåller alla virtuella datorer. I allmänhet grupperar du resurser baserat på deras livstid och vem som ska hantera dem. De virtuella datorerna i den här arkitekturen kräver [accelererade nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Det är en Azure-funktion som ger konsekvent, extremt låg nätverksfördröjning via en-root I/O-virtualisering (SR-IOV) till en virtuell dator.

Varje virtuell Azure-dator distribueras till ett virtuellt nätverk som har undernät: huvud, Gluster FS-klientdel (gfsfe), Gluster FS-serverdel (bfsbe), DB2 pureScale (db2be) och DB2 pureScale front (db2fe). Installationsskriptet skapar också de primära [nätverkskorten](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) på de virtuella datorerna i huvudundernätet.

Använd [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) för att begränsa nätverkstrafiken i det virtuella nätverket och för att isolera undernäten.

På Azure måste DB2 pureScale använda TCP/IP som nätverksanslutning för lagring.

## <a name="next-steps"></a>Nästa steg

-   [Distribuera den här arkitekturen på Azure](deploy-ibm-db2-purescale-azure.md)
