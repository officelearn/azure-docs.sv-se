---
title: Check lista för SAP-arbetsbelastnings planering och distribution | Microsoft Docs
description: Check lista för planering och distribution av SAP-arbetsbelastningar på Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: abe04a88f3337ed26b98815b5633fd5d719b7ce3
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234229"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>SAP-arbetsbelastning på Azure planering och distribution check lista 

Den här check listan är utformad för kunder som flyttar sina SAP NetWeaver-, S/4HANA-och hybris-program till Azure-infrastruktur som en tjänst.  Den här check listan bör granskas av en kund-och/eller SAP-partner under projektets varaktighet. Det är viktigt att många av kontrollerna utförs i början av projektet och i planerings fasen. När distributionen är genomförd kan det bli komplext att ändra elementen i distribuerade Azure-infrastrukturer eller SAP-program versioner. Granska den här check listan i viktiga mil stolpar i ett projekt.  Små problem kan identifieras innan de blir stora problem och det finns tillräckligt med tid för att återställa och testa eventuella nödvändiga ändringar. Check listan på några sätt som anspråk ska slutföras. Beroende av din enskilda situation kan det finnas många fler kontroller som måste utföras. 

Check listan monteras inte med uppgifter som är oberoende av Azure.  Exempel: SAP-programgränssnitt ändras under en över gången till det offentliga Azure-molnet eller till en värd leverantör.    

Den här check listan kan också användas för redan distribuerade system. Nya funktioner som Skrivningsaccelerator, Tillgänglighetszoner och nya VM-typer kan ha lagts till sedan du distribuerade.  Det är därför användbart att granska check listan regelbundet för att se till att du är medveten om nya funktioner i Azure-plattformen. 

## <a name="project-preparation-and-planning-phase"></a>Projekt förberedelse och planerings fas
I den här fasen planeras migreringen av SAP-arbetsbelastningar till det offentliga Azure-molnet. Den minsta uppsättningen entiteter och objekt som diskuteras och den definierade listan, t. ex.:

1. Design dokument på hög nivå – det här dokumentet ska innehålla:
    1. Den aktuella inventeringen av SAP-komponenter och program och mål program inventering på Azure
    2. Skapa och arbeta via en tilldelnings mat ris (RACI) som definierar ansvars områden och tilldelningar för de olika berörda parterna. Börja på hög nivå och arbeta med fler och fler detaljerade nivåer genom strömning av planering och första distributioner
    2. En hög nivå lösnings arkitektur
    3. Beslut i Azure-regioner att distribuera till. Om du vill ha en lista över Azure-regioner kontrollerar du [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). För tjänster som är tillgängliga i alla Azure-regioner, se artikeln [produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/)
    4. Nätverks arkitektur för att ansluta lokalt till Azure. Börja med att bekanta dig med den [virtuella Data Center skissen för Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Säkerhets principer för att köra data för hög affärs effekt i Azure. För att läsa material som börjar med [Azures säkerhets dokumentation](https://docs.microsoft.com/azure/security/)
2.  Tekniskt design dokument – som innehåller:
    1.  Ett lösnings block diagram 
    2.  Storleks ändring för beräknings-, lagrings-och nätverks komponenter i Azure. Information om SAP-storlek för virtuella Azure-datorer finns i SAP support NOTE [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Affärs kontinuitet och katastrof återställnings arkitektur
    4.  Detaljerade versioner av support paket för operativ system, databaser, kernel och SAP. Ingen OS-version som stöds av SAP NetWeaver eller S/4HANA stöds i virtuella Azure-datorer. Detsamma gäller för DBMS-versioner. Det är obligatoriskt att följande källor kontrol leras för att justera och vid behov uppgradera SAP-versioner, DBMS-utgåvor eller OS-versioner för att vara i ett fönster med SAP och Azure som stöds. Det är obligatoriskt att du är i de versions kombinationer SAP och Azure som stöds för att få fullständig support av SAP och Microsoft. Vid behov måste du planera för att uppgradera några av program komponenterna. Mer information om stöd för SAP-, OS-och DBMS-programvara finns dokumenterade på följande platser:
        1.  [#1928533](https://launchpad.support.sap.com/#/notes/1928533)för SAP support Note. Den här kommentaren definierar de lägsta OS-versioner som stöds i virtuella Azure-datorer. Den definierar också de lägsta databas versioner som krävs för de flesta icke-HANA-databaser. Anteckningen visar också SAP-storlekarna för de olika Azure VM-typer som stöds av SAP.
        2.  [#2039619](https://launchpad.support.sap.com/#/notes/2039619)för SAP support Note. Kommentaren definierar Oracle-stödmatrisen på Azure. Inse att Oracle endast stöder Windows och Oracle Linux som gäst operativ system i Azure för SAP-arbetsbelastningar. Den här support instruktionen gäller även för SAP-program skiktet som kör SAP-instanser. Oracle har dock inte stöd för hög tillgänglighet för SAP Central Services i Oracle Linux via pacemaker. Om du behöver hög tillgänglighet för ASCS på Oracle Linux måste du utnyttja SIOS Protection Suite för Linux. Detaljerad information om SAP-certifieringar finns i information [om SAP-support #1662610-support för SIOS Protection Suite för Linux](https://launchpad.support.sap.com/#/notes/1662610). För Windows stöds den SAP-lösning som stöds av Windows-redundanskluster för SAP Central Services tillsammans med Oracle som DBMS-lager. 
        3.  Stöd för SAP-support [#2235581](https://launchpad.support.sap.com/#/notes/2235581) för att hämta stödmatrisen för SAP HANA på olika OS-versioner
        4.  SAP HANA virtuella Azure-datorer som stöds och [Hana-stora instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) visas [här](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Produkt tillgänglighets mat ris för SAP](https://support.sap.com/en/)
        6.  Andra SAP-anteckningar för andra SAP-produkter  
    5.  Vi rekommenderar strikta design system på 3 nivåer för SAP-produktionssystem. Det rekommenderas inte att kombinera ASCS + APP-servrar på samma virtuella dator.  Användning av klusterkonfigurationer med flera-SID för SAP Central Services stöds med Windows som gäst operativ system på Azure. Det går inte att använda klusterkonfigurationer för flera säkerhets-ID: t för SAP Central Services med Linux-operativsystem på Azure. Dokumentation för Windows gäst operativ system fall finns i:
        1.  [SAP ASCS/SCS-instans multi-SID hög tillgänglighet med Windows Server-redundanskluster och delad disk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [SAP ASCS/SCS-instans multi-SID hög tillgänglighet med kluster för växling vid fel i Windows Server och fil resurs på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Hög tillgänglighet och katastrof återställnings arkitektur
        1.  Definiera baserat på RTO och återställnings arkitektur för hög tillgänglighet och haveri beredskap måste se ut som
        2.  För hög tillgänglighet i samma zon, kontrol lera vad det önskade DBMS: t har att erbjuda i Azure. De flesta DBMS erbjuder synkrona metoder för synkron snabb växling, som vi rekommenderar för produktions system. Kontrol lera också den SAP-relaterade dokumentationen för de olika databaserna som börjar med [överväganden för Azure Virtual Machines DBMS-distribution för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) -arbetsbelastningar och relaterade dokument
            1.  Att använda Windows-tjänsten för redundanskluster med delad disk konfiguration för DBMS-skiktet som, till exempel, som beskrivs för SQL Server [här](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) stöds **inte** . I stället lösningar som:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  För haveri beredskap i olika Azure-regioner, kontrol lera vilka möjligheter som erbjuds av olika DBMS-leverantörer. De flesta av dem stöder asynkron replikering eller logg leverans
        4.  För SAP-programnivå definierar du om du skulle köra dina Business regression test-system, vilket är idealiskt för dina produktions distributioner, i samma Azure-region eller i din DR-region. I det senare fallet kan du rikta in dig på affärs Regressions systemet som DR-mål för din produktion
        5.  Om du väljer att inte placera de icke-produktions systemen på DR-platsen kan du se Azure Site Recovery som en livskraftig metod för att replikera SAP-Programskiktet till Azure DR-regionen. Se även [Konfigurera haveri beredskap för en distribution med flera nivåer av SAP NetWeaver-appar](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Om du bestämmer dig för att använda en kombinerad HA-eller DR-konfiguration kan du med hjälp av [Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview) göra dig bekant med de Azure-regioner där Tillgänglighetszoner är tillgängliga och med begränsningar som kan introduceras genom ökad nätverks fördröjning mellan två Tillgänglighetszoner  
3.  Kunden/partnern bör skapa en inventering av alla SAP-gränssnitt (SAP och icke-SAP). 
4.  Design av grund tjänster – designen – den här designen inkluderar objekt som
    1.  Active Directory och DNS-design
    2.  Nätverkstopologi i Azure och tilldelning av olika SAP-system
    3.  [Rollbaserad åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview) struktur för dina olika team som hanterar infrastruktur-och SAP-program i Azure
    3.  Topologi för resurs grupp 
    4.  [Tagga strategi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Namngivnings konvention för virtuella datorer och andra infrastruktur komponenter och/eller logiska namn
5.  Microsoft Premier Support-avtal – identifiera teknisk konto hanterare (TAM). För support krav av SAP Read SAP support not [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Definiera antalet Azure-prenumerationer och kärn kvot för de olika prenumerationerna. [Öppna support förfrågningar för att öka kvoterna för Azure-prenumerationer om](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) det behövs 
7.  Plan för data minskning och datamigrering för att migrera SAP-data till Azure. För SAP NetWeaver-system har SAP rikt linjer för hur du håller volymen av ett stort antal data begränsade. SAP publicerade [den här Profound-guiden](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) om data hantering i SAP ERP-system. En del innehåll gäller dock för NetWeaver-och S/4HANA-system i allmänhet.
8.  Definiera och bestäm metod för automatisk distribution. Målet för Automation efter infrastruktur distributioner på Azure är att distribuera på ett deterministiskt sätt och få deterministiska resultat. Många kunder använder Power Shell-eller CLI-baserade skript. Men det finns flera tekniker med öppen källkod som kan användas för att distribuera Azure-infrastruktur för SAP och till och med installera SAP-program. Du hittar exempel i GitHub:
    1.  [Automatiserade SAP-distributioner i Azure-molnet](https://github.com/Azure/sap-hana)
    2.  [SAP HANA installation](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definiera en vanlig gransknings takt för design och distribution mellan dig som kund, system integrerare, Microsoft och andra berörda parter

 
## <a name="pilot-phase-strongly-recommended"></a>Pilot fas (rekommenderas starkt)
 
Piloten kan köras före eller parallellt med projekt planering och-förberedelse. Fasen kan också användas för att testa metoder och design som görs i planerings-och förberedelse fasen. Pilot fasen kan sträckas ut till en riktig koncept bevis. Vi rekommenderar att du ställer in och validerar en fullständig HA/DR-lösning samt säkerhets design under en pilot distribution. I vissa kund fall kan skalbarhets test också utföras i den här fasen. Andra kunder använder distribution av system för SAP sandbox-system som pilot fas. Vi förutsätter att du har identifierat ett system som du vill migrera till Azure i syfte att köra en pilot.

1. Optimera data överföring till Azure. Mycket beroende av kund ärende överföring via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) från lokalt var snabbast om Express-kretsen hade tillräckligt med bandbredd. Med andra kunder går det snabbare att gå igenom Internet
2. I händelse av en SAP heterogen plattforms migrering som omfattar export och import av databas data, test och optimering av export-och import faser. För stora migreringar som involverar SQL Server som mål plattform hittar du rekommendationer [här](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Du kan använda metoden för migrerings-och SWPM om du inte behöver en kombinerad versions uppgradering eller [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) -process när du kombinerar migreringen med en SAP-release-uppgradering och uppfyller vissa käll-och mål DBMS plattforms kombinationer som dokumenterad, till exempel i [DMO (Database migration option) för SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exportera till källa, exportera fil överföring till Azure och importera prestanda.  Maximera överlappning mellan export och import
   2.  Utvärdera databasen för databaser mellan mål-och mål plattform för att avspegla storleken på infrastrukturens storlek    
   3.  Validera och optimera tids inställningar 
3. Teknisk validering 
   1. VM-typer
      1.  Verifiera resurserna på SAP-supporten, SAP HANA maskin varu katalogen och SAP PAM igen för att se till att det inte fanns några ändringar i virtuella datorer som stöds för Azure, OS-versioner som stöds för dessa VM-typer och stödda SAP-och DBMS-versioner
      2.  Verifiera storleken på programmet och den infrastruktur som du distribuerar på Azure. Om du flyttar befintliga program kan du ofta härleda de nödvändiga SAPS från infrastrukturen du använder och [webb sidan för SAP-benchmark](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) och jämföra den med SAPS-numren som anges i SAP support NOTE [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Håll också [den här artikeln](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) i åtanke
      3.  Utvärdera och testa storleken på dina virtuella Azure-datorer angående maximalt lagrings data flöde och nätverks data flöde för de olika VM-typer som du valde i planerings fasen. Du hittar dessa data i:
          1.  [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Det är viktigt att tänka på det **maximala disk data flöde** som inte har cachelagrats för storleks ändring
          2.  [Storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Det är viktigt att tänka på det **maximala disk data flöde** som inte har cachelagrats för storleks ändring
   2. Storage
      1.  Använd [Azure standard SSD Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) som minst för virtuella datorer som representerar SAP-program och för icke-högpresterande DBMS-distribution
      2.  Vi rekommenderar att du inte använder [Azure standard HDD diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) i allmänhet
      2.  Använd [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) för virtuella DBMS-datorer som är fjärrpresterade för fjärr styrning
      2.  Använd [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Använd Azure Skrivningsaccelerator för DBMS-loggfiler med M-serien. Tänk på begränsningar och användning av Skriv acceleratorer som dokumenteras i [Skrivningsaccelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  För olika DBMS-typer kontrollerar du den [allmänna SAP-relaterade DBMS-dokumentationen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) och de DBMS-särskilt dokument som det generiska dokumentet pekar på
      5.  För SAP HANA dokumenteras mer information i [SAP HANA infrastruktur konfiguration och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Montera aldrig Azure-datadiskarna till en virtuell Azure Linux-dator med hjälp av enhets-ID. Använd i stället den universella unika identifieraren (UUID). Var försiktig när du använder grafiska verktyg för att montera Azure Data disks, till exempel. Kontrol lera posterna i/etc/fstab för att se till att diskarna monteras med hjälp av UUID
          1.  Mer information finns [här](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Nätverk
      1.  Testa och utvärdera din VNet-infrastruktur och distributionen av dina SAP-program över eller inom de olika virtuella Azure-nätverken
          1.  Utvärdera belastningen på hubb och Ekers virtuella nätverks arkitektur eller mikrosegmentering i ett enda virtuellt Azure-nätverk baserat på
              1.  Kostnader på grund av data utbyte mellan [peered Azure-virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). För kostnader kontrollerar [Virtual Network priser](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Dra nytta av snabb från koppling mellan virtuella Azure-nätverk i jämförelse med att ändra NSG för att isolera ett undernät i ett virtuellt nätverk, i de fall där program eller virtuella datorer som finns i ett undernät i det virtuella nätverket blev en säkerhets risk
              3.  Central loggning och granskning av nätverks trafik mellan lokalt, utanför världen och det virtuella Data Center som du har byggt i Azure
          2.  Utvärdera och testa data Sök väg mellan SAP-program skiktet och SAP-DBMS-skiktet. 
              1.  Placering av [virtuella Azure-nätverk](https://azure.microsoft.com/solutions/network-appliances/) i kommunikations vägen mellan SAP-programmet och DBMS-skiktet i ett SAP NetWeaver-, hybris-eller S/4HANA-baserade SAP-system stöds inte alls
              2.  Det går inte att placera SAP-Programskiktet och SAP-DBMS i olika virtuella Azure-nätverk som inte är peer-kopplingar
              3.  [Azure-grupperna och NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview) stöds för att definiera vägar mellan SAP-program skiktet och SAP-DBMS-skiktet
          3.  Kontrol lera att [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som används i SAP-Programskiktet och SAP-DBMS-skiktet. Tänk på att olika OS-nivåer behövs för att stödja accelererat nätverk i Azure:
              1.  Windows Server 2012 R2 eller senare versioner
              2.  SUSE Linux 12 SP3 eller senare versioner
              3.  RHEL 7,4 eller senare versioner
              4.  Oracle Linux 7,5. Med RHCKL-kärnan måste versionen vara 3.10.0-862.13.1. el7. Du måste använda Oracle UEK kernel Release 5
          4.   Testa och utvärdera nätverks fördröjningen mellan SAP-programlager VM och DBMS VM enligt SAP support NOTE [#500235](https://launchpad.support.sap.com/#/notes/500235) och SAP support NOTE [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultatet mot nätverks fördröjnings vägledningen för SAP support NOTE [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverks fördröjningen ska vara i det måttliga och bästa intervallet. Undantag gäller trafik mellan virtuella datorer och HANA stora instans enheter som dokumenteras [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Kontrol lera att ILB-distributioner har kon figurer ATS för att använda direkt Server RETUR. Den här inställningen minskar svars tiden i de fall där Azure-ILB används för konfigurationer med hög tillgänglighet i DBMS-skiktet
          6.   Om du använder Azure Load Balancer tillsammans med Linux-gäst operativ system, kontrollerar du att parametern för Linux-nätverket **net. IPv4. TCP _timestamps** har värdet **0**. Mot rekommendationerna i äldre versioner av SAP NOTE [#2382421](https://launchpad.support.sap.com/#/notes/2382421). SAP-anteckningen uppdateras för att avspegla det faktum att parametern måste anges till 0 för att fungera tillsammans med Azure Load Balancer.
          7.   Överväg att använda [Azure närhets placerings grupp](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) enligt beskrivningen i artikeln [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md) för att få den optimala nätverks fördröjningen.
   4. Distributioner av hög tillgänglighet och haveri beredskap. 
      1. Om du distribuerar SAP-programlagret utan att definiera en specifik Azure-tillgänglighets zon, se till att alla virtuella datorer som kör instansen av SAP-dialog instans eller mellan instanser av ett enda SAP-system distribueras i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   Om du inte behöver hög tillgänglighet för de centrala SAP-tjänsterna och DBMS kan de här virtuella datorerna distribueras till samma tillgänglighets uppsättning som SAP-program skiktet
      2. Om du skyddar SAP Central Services och DBMS-skiktet för hög tillgänglighet med passiva repliker, måste du ha två noder för SAP Central Services i en separat tillgänglighets uppsättning och den två DBMS-noden i en annan tillgänglighets uppsättning
      3. Om du distribuerar till Azure-tillgänglighetszoner kan du inte utnyttja tillgänglighets uppsättningar. Du måste dock se till att du distribuerar aktiva och passiva centrala tjänster-noder i två olika Tillgänglighetszoner, vilket visar den minsta svars tiden mellan zoner.
         1.   Tänk på att du behöver använda [Azure standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) för att upprätta Windows-eller pacemaker-redundanskluster för skiktet DBMS och SAP Central Services i Tillgänglighetszoner. [Basic-Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kan inte användas för zonindelade-distributioner 
   5. Tids gräns inställningar
      1. Kontrol lera SAP NetWeaver Developer-spår för olika SAP-instanser och se till att inga anslutnings avbrott mellan server-och SAP-arbetsprocesserna anges. Dessa anslutnings avbrott kan undvikas genom att ange dessa två register parametrar:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – Se även [den här artikeln](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – Se även [den här artikeln](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. För att undvika tids gränser för GUI mellan ett lokalt distribuerat SAP GUI-gränssnitt och SAP-program som distribueras i Azure, kontrollerar du om följande parametrar har angetts i standardvärdet. PFL eller instans profilen:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. För att förhindra störningar av etablerade anslutningar mellan SAP-kön och SAP-workprocesse måste parametern placera/encni/set_so_keepalive anges till "true". Se även [SAP obs #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Om du använder en konfiguration av Windows-redundanskluster kontrollerar du att tiden för att reagera på noder som inte svarar är korrekt inställd för Azure. Tröskelvärdena för Microsoft-artikel för att [Justera redundanskluster](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) visar en lista över parametrar och hur dessa påverkar redundansväxlingen sensitivities. Förutsatt att klusternoderna finns i samma undernät bör följande parametrar ändras:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Testa återställnings procedurerna för hög tillgänglighet och haveri beredskap
   1. Simulera redundansväxlingen genom att stänga av virtuella datorer (Windows gäst operativ system) eller placera operativ system i panik-läge (Linux gäst operativ system) för att ta reda på om dina redundanskonfiguration fungerar som de ska. 
   2. Mät tiden det tar att utföra en redundansväxling. Om tiderna tar för lång tid kan du tänka på följande:
      1.   För SUSE Linux använder du SBD-enheter i stället för Azures staket-agenten för att påskynda redundansväxlingen
      2.   För SAP HANA, om ombelastningen av data tar för lång tid att etablera mer lagrings bandbredd
   3. Testa Backup/Restore Sequence och timing och justera om det behövs. Kontrol lera att det inte bara finns tillräckligt med säkerhets kopierings tider. Prova också att återställa och ta tid på att återställa aktiviteter. Se till att återställnings tiderna finns i RTO-service avtal där RTO förlitar sig på en databas eller återställnings process för virtuella datorer
   4. Testa över en regions DR-funktion och arkitektur
5. Säkerhets kontroller
   1.  Testa giltigheten för den RBAC-arkitektur (Azure Role based Access) som du har implementerat. Målet är att avgränsa och begränsa åtkomst och behörigheter för olika team. Som exempel bör SAP basen-team medlemmar kunna distribuera virtuella datorer och tilldela diskar från Azure Storage till ett specifikt virtuellt Azure-nätverk. SAP-basen bör dock inte kunna skapa egna virtuella nätverk eller ändra inställningarna för befintliga virtuella nätverk. På den andra sidan bör medlemmar i nätverks teamet inte kunna distribuera virtuella datorer till virtuella nätverk där SAP-program och virtuella DBMS-datorer körs. Eller om medlemmar i nätverks teamet ska kunna ändra attribut för virtuella datorer eller till och med ta bort virtuella datorer eller diskar.  
   2.  Kontrol lera att reglerna för [NSG och ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) fungerar som förväntat och kontrol lera de skyddade resurserna
   3.  Kontrol lera att alla resurser som behöver krypteras är krypterade. Definiera och kör processer för att säkerhetskopiera certifikat, lagra och få åtkomst till dessa certifikat och återställa de krypterade entiteterna. 
   4.  Använd [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) och/eller för OS-diskar där det är möjligt från en OS-support punkt i vyn
   5.  Kontrol lera att inte för många lager med kryptering har använts. Det är en begränsad idé att använda Azure Disk Encryption och sedan på en av de olika krypterings metoderna i DBMS-databasen
6. Prestanda testning
   1.  I SAP baserat på SAP-spårning och mått, jämför de 10 främsta rapporterna med den aktuella implementeringen där tillämpligt 
   2.  I SAP baserat på SAP-spårning och mått, jämför de 10 främsta batch-jobben med den aktuella implementeringen där tillämpligt 
   3.  I SAP baserat på SAP-spårning och mått, jämför data överföringar via gränssnitt i SAP-systemet. Fokusera på gränssnitt där du vet att överföringen nu sker mellan olika platser, till exempel att gå från lokala platser till Azure 


## <a name="non-production-phase"></a>Ej produktions fas 
I den här fasen antar vi att efter en lyckad pilot eller PoC börjar du distribuera SAP-system som inte är produktion till Azure. All information och erfarenhet av koncept bevis bör anpassas till en sådan distribution. Alla kriterier och steg som anges i PoC gäller även i den här typen av distributioner. I den här fasen distribuerar du vanligt vis utvecklings system, enhets test system och affärs Regressions test system till Azure. Vi rekommenderar att minst ett icke-produktionssystem på en SAP-programlinje har fullständig konfiguration för hög tillgänglighet eftersom det framtida produktions systemet ska ha. Ytterligare åtgärder som du måste ta hänsyn till under den fasen är:  

1.  Innan du flyttar system från den gamla plattformen till Azure samlar du in resurs förbruknings data, t. ex. processor användning, data flöde för lagring och IOPS. Särskilt från DBMS-skiktet, men även från program skikts enheterna. Mät även nätverks-och lagrings svars tider.
2.  Registrera systemets tillgänglighets användnings tids mönster. Målet är att ta reda på om icke-produktionssystem måste vara tillgängliga dygnet runt eller om det finns andra system än produktions system som kan stängas ned under vissa faser i en vecka eller månad
3.  Testa och definiera om du vill skapa egna OS-avbildningar för dina virtuella datorer i Azure eller om du vill använda en avbildning från Azures avbildnings Galleri. Om du använder en avbildning från Azure-galleriet kontrollerar du att du tar rätt avbildning som motsvarar support kontraktet med din OS-leverantör. För vissa OS-leverantörer erbjuder Azure gallerier dig att ta med dina egna licens avbildningar. För andra OS-avbildningar ingår support i det pris som anges av Azure. Om du väljer att skapa egna OS-avbildningar kan du hitta dokumentation i de här artiklarna:
    1.  Du kan bygga en generaliserad avbildning av en virtuell Windows-dator som distribuerats i Azure baserat på [den här dokumentationen](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Du kan bygga en generaliserad avbildning av en virtuell Linux-dator som distribuerats i Azure baserat på [den här dokumentationen](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Om du använder SUSE-och Red Hat Linux-avbildningar från Azures Galleri för virtuella Azure-datorer, måste du använda avbildningarna för SAP som tillhandahålls av Linux-leverantörerna i Azure VM-galleriet
4.  Kontrol lera att du uppfyller support kraven SAP har avseende Microsofts support avtal. Information finns i [#2015553](https://launchpad.support.sap.com/#/notes/2015553)för SAP-support. För HANA-stora instanser, se de dokumenterade [registrerings kraven](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Kontrol lera att rätt personer får [planerat underhålls meddelanden](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), så att du kan välja drift stopp och omstart av virtuella datorer i tid
5.  Läs mer ständigt Azure-dokumentation om Microsoft-presentationer på kanaler som [channel9](https://channel9.msdn.com/) för nya funktioner som kan gälla för dina distributioner
6.  Kontrol lera SAP-anteckningar som är relaterade till Azure, till exempel support NOTE [#1928533](https://launchpad.support.sap.com/#/notes/1928533) för nya VM SKU: er eller operativ system och DBMS-versioner som stöds. Jämför nya VM-typer mot äldre VM-typer i prissättning, så att du kan distribuera virtuella datorer med bästa pris/prestanda-förhållande
7.  Verifiera resurserna på SAP-supporten, SAP HANA maskin varu katalogen och SAP PAM igen för att se till att det inte fanns några ändringar i virtuella datorer som stöds för Azure, OS-versioner som stöds på de virtuella datorerna och stödda SAP-och DBMS-versioner
8.  [Här](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) hittar du nya Hana-certifierade SKU: er i Azure och jämför priserna med de som du planerade och slutligen vill ändra för att få enheterna bättre pris 
9.  Anpassa dina distributions skript för att utnyttja nya VM-typer och införliva nya funktioner i Azure som du vill använda
10. Efter distributionen av infrastrukturen kan du testa och utvärdera nätverks fördröjningen mellan SAP-Programskiktet VM och DBMS VM enligt SAP support NOTE [#500235](https://launchpad.support.sap.com/#/notes/500235) och SAP support NOTE [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultatet mot nätverks fördröjnings vägledningen för SAP support NOTE [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverks fördröjningen ska vara i det måttliga och bästa intervallet. Undantag gäller trafik mellan virtuella datorer och HANA stora instans enheter som dokumenteras [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Se till att ingen av de begränsningar som anges i [överväganden för azure Virtual Machines DBMS-distribution för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) -arbetsbelastningar och [SAP HANA infrastruktur konfiguration och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) gäller för din distribution
11. Se till att de virtuella datorerna distribueras med rätt [placerings grupp för Azure närhet](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) enligt beskrivningen i artikeln [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md)
11. Utför alla andra kontroller som anges i koncept bevis fasen innan arbets belastningen tillämpas
12. När arbets belastningen används registrerar du resurs förbrukningen för dessa system i Azure och jämför med de poster som du fick från din gamla plattform. Justera storleken på den virtuella datorn för framtida distributioner om du ser att du har större skillnader. Tänk på att när det gäller downsizing, lagring och nätverks bandbredder på en virtuell dator kommer också att minskas:
    1.  [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Arbeta med system kopierings funktioner och-processer. Målet är att göra det enkelt för dig att kopiera ett utvecklings system eller ett test system, så att projekt teamen kan snabbt få nya system. Överväg [SAP Lama](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) som ett verktyg som utför sådana uppgifter.
14. Optimera och finslipa teamets Azure-roll baserat på åtkomst, behörigheter och processer för att säkerställa att du har en del av tullen på den ena sidan. På den andra sidan vill du att alla team ska kunna utföra sina uppgifter i Azure-infrastrukturen.
15. Öva, testa och dokumentera metoder för hög tillgänglighet och haveri beredskap för att göra det möjligt för personalen att utföra sådana uppgifter. Identifiera brister och anpassa nya Azure-funktioner som du integrerar i dina distributioner

 
## <a name="production-preparation-phase"></a>Förberedelse fas för produktion 
I den här fasen vill du samla in alla erfarenheter och all information om dina distributioner utan produktion och tillämpa dem i framtida produktions distributioner. Förutom faserna tidigare måste du också förbereda arbetet med data överföringen mellan din aktuella värd plats och Azure. 

1.  Arbeta genom nödvändiga SAP-release-uppgraderingar av dina produktions system innan du flyttar till Azure
2.  Samtycker till företags ägarna om de funktionella och affärsrelaterade test som måste utföras efter migreringen av produktions systemet
    1.  Se till att alla dessa tester körs med käll systemen på den aktuella värd platsen. Du vill undvika att tester utförs för första gången när systemet flyttas till Azure
2.  Testa processen för produktion av produktion i Azure. Om du inte flyttar alla produktions system till Azure i samma tidsram skapar du grupper av produktions system som måste finnas på samma värd plats. Träna och testa datamigrering. Lista med vanliga metoder som:
    1.  Använda DBMS-metoder som säkerhets kopiering/återställning i kombination med SQL Server AlwaysOn, HANA-systemreplikering eller logg överföring till dirigering och synkronisering av databas innehåll i Azure
    2.  Använd säkerhets kopiering/återställning för mindre databaser
    3.  Använd en SAP-migrering som implementeras i SAP SWPM-verktyget för att utföra heterogena migreringar
    4.  Använd processen [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) om du behöver kombinera med en uppgradering av SAP-utgåvor. Tänk på att det inte finns stöd för alla kombinationer mellan käll-och mål-DBMS. Mer information finns i den aktuella SAP-supporten för de olika versionerna av DMO. Till exempel, [DMO (Database migration option) av SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Testa om data överföring via Internet eller via ExpressRoute är bättre i data flödet om du behöver flytta säkerhets kopior eller SAP-exportfiler. När det gäller att flytta data via Internet kan du behöva ändra några av dina NSG-/grupperna-säkerhetsregler som du måste ha i stället för framtida produktions system
3.  Innan du flyttar system från den gamla plattformen till Azure samlar du in resurs förbruknings data, t. ex. processor användning, data flöde för lagring och IOPS. Särskilt från DBMS-skiktet, men även från program skikts enheterna. Mät även nätverks-och lagrings svars tider.
4.  Verifiera resurserna på SAP-supporten, SAP HANA maskin varu katalogen och SAP PAM igen för att se till att det inte fanns några ändringar i virtuella datorer som stöds för Azure, OS-versioner som stöds på de virtuella datorerna och stödda SAP-och DBMS-versioner 
4.  Anpassa distributions skript till de senaste ändringarna du har valt för VM-typer och Azure-funktioner
5.  När du har distribuerat infrastrukturen och programmet går du igenom en serie kontroller för att verifiera:
    1.  Rätt VM-typer har distribuerats med rätt attribut och lagrings storlekar
    2.  Kontrol lera att de virtuella datorerna finns på rätt och önskade OS-versioner och-korrigeringar och är enhetliga
    3.  Kontrol lera att härdningen av de virtuella datorerna är lika obligatorisk och enhetlig
    4.  Kontrol lera att rätt program versioner och uppdateringar har installerats och distribuerats
    5.  De virtuella datorerna distribuerades till Azures tillgänglighets uppsättningar som planerat
    6.  Azure Premium Storage användes för svars känsliga diskar eller där det [enda service nivå avtalet för VM på 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) krävs
    7.  Sök efter korrekt distribution av Azure Skrivningsaccelerator
        1.  Kontrol lera att i VM-, lagrings utrymmen eller stripe-uppsättningar har skapats korrekt på diskar som behöver stöd för Azure Skrivningsaccelerator
            1.  Kontrol lera [Konfigurera programvaru-RAID på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Kontrol lera [Konfigurera LVM på en virtuell Linux-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) användes exklusivt
    9.  De virtuella datorerna har distribuerats till rätt tillgänglighets uppsättningar och Tillgänglighetszoner
    10. Kontrol lera att [Azure-accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) är aktiverat på de virtuella datorer som används i SAP-programlagret och SAP DBMS-skiktet
    11. Ingen placering av [virtuella Azure](https://azure.microsoft.com/solutions/network-appliances/) -nätverksanslutningar i kommunikations vägen mellan SAP-programmet och DBMS-skiktet i ett SAP NetWeaver-, hybris-eller S/4HANA-baserade SAP-system
    12. GRUPPERNA-och NSG-regler tillåter kommunikation som önskad och planerad och blockerar kommunikation där det behövs
    13. Tids gräns inställningarna enligt beskrivningen tidigare har angetts korrekt
    14. Se till att de virtuella datorerna distribueras med rätt [placerings grupp för Azure närhet](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) enligt beskrivningen i artikeln [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md)
    15. Testa och utvärdera nätverks fördröjningen mellan SAP-programlager VM och DBMS VM enligt SAP support NOTE [#500235](https://launchpad.support.sap.com/#/notes/500235) och SAP support NOTE [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Utvärdera resultatet mot nätverks fördröjnings vägledningen för SAP support NOTE [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Nätverks fördröjningen ska vara i det måttliga och bästa intervallet. Undantag gäller trafik mellan virtuella datorer och HANA stora instans enheter som dokumenteras [här](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Kontrol lera om krypteringen har distribuerats vid behov och med krypterings metoden nödvändig
    16. Kontrol lera om gränssnitt och andra program kan ansluta den nyligen distribuerade infrastrukturen
6.  Skapa en Spelbok för att reagera på planerat underhåll i Azure. Definiera ordningen på de system och virtuella datorer som ska startas om vid planerat underhåll
    

## <a name="go-live-phase"></a>Gå live-fasen
För Go-Live-fasen måste du kontrol lera att du följer din spel böcker som du utvecklade i tidigare faser. Utför de steg som du har testat och tränat. Acceptera inte de senaste minuters ändringarna i konfigurationer och process. Förutom att vidta följande åtgärder:

1. Kontrol lera att Azure Portal övervakning och andra övervaknings verktyg fungerar.  Rekommenderade verktyg är perfmon (Windows) eller SAR (Linux): 
    1.  CPU-räknare 
        1.  Genomsnittlig CPU-tid – total (all CPU)
        2.  Genomsnittlig CPU-tid – varje enskild processor (so 128 processorer på M128 VM)
        3.  CPU Time-kernel – varje enskild processor
        4.  CPU-tid för användare – varje enskild processor
    5.  Minne 
        1.  Ledigt minne
        2.  Minnes sidan i/SEK
        3.  Minnes sida ut/SEK
    4.  Disk 
        1.  Disk läsning KB/s – per enskild disk 
        2.  Disk läsningar/s – per enskild disk
        3.  Disk läsning MS/Read – per enskild disk
        4.  Disk skrivning KB/s – per enskild disk 
        5.  Disk skrivning/s – per enskild disk
        6.  Disk skrivning MS/Read – per enskild disk
    5.  Nätverk 
        1.  Nätverks paket i/SEK
        2.  Nätverks paket ut/SEK
        3.  Nätverks-KB i/SEK
        4.  Nätverks-KB ut/SEK 
2.  Efter migreringen av data utför du alla verifieringstester som du har samtyckt till företagets ägare. Acceptera bara verifierings test resultat där du har resultat för de ursprungliga käll systemen
3.  Kontrol lera om gränssnitt fungerar och om andra program kan kommunicera med de nyligen distribuerade produktions systemen
4.  Kontrol lera transport-och korrigerings systemet via SAP Transaction STMS
5.  Utföra säkerhets kopior av databasen när systemet lanseras för produktion
6.  Säkerhetskopiera virtuella datorer i SAP-Programskiktet när systemet lanseras för produktion
7.  För SAP-system som inte var en del av den aktuella Go-Live-fasen, men som kommunicerar med SAP-systemen som du flyttade till Azure i den här Go-Live-fasen, måste du återställa värd namns bufferten i SM51. Det här steget kommer att ta bort de gamla cachelagrade IP-adresserna som är kopplade till namnen på de program instanser som du flyttade till Azure  


## <a name="post-production"></a>Publicera produktion
I den här fasen är det allt om övervakning, drift och administration av systemet. Från en SAP-punkt i vyn gäller vanliga uppgifter som du behövde utföra med din gamla värd plats. De Azure-aktiviteter du vill göra är:

1. Analysera Azure-fakturor för system med hög laddning
2. Optimera pris/prestanda-effektivitet på VM-sidan och lagrings Sidan
3. Optimera tids system kan stängas av  


## <a name="next-steps"></a>Nästa steg
Läs dokumentationen:

- [Azure Virtual Machines planera och implementera SAP-NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

