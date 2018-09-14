---
title: SAP Business en på Azure-datorer | Microsoft Docs
description: SAP Business en på Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca089672cf645af58952205dada66aa96ba0b65d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578251"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business en på Azure-datorer
Det här dokumentet innehåller riktlinjer för att distribuera SAP Business One på Azure Virtual Machines. Dokumentationen är inte en ersättning för dokumentationen för installation av ett företag för SAP. Dokumentationen bör omfatta grundläggande riktlinjer för planering och distribution för Azure-infrastrukturen att köra Business One program på.

Företag som har stöd för två olika databaser:
- SQLServer – Se [SAP Obs! #928839 - versionen planera för Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - för exakta SAP Business One stöd matrix för SAP HANA, Kolla in den [SAP produkten tillgänglighet matris](https://support.sap.com/pam)

Om SQL Server, grundläggande distributionsöverväganden enligt beskrivningen i den [DBMS i Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) gäller. för SAP HANA nämns överväganden i det här dokumentet.

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenterna:

- [Azure virtuella datorer i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Azure-datorer i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-nätverk och virtuella datornätverk hantering med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Azure-nätverk och virtuella nätverk med CLI ](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Hantera Azure-diskar med Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Även om du är intresserad av företag en, dokumentet [Azure Virtual Machines planering och implementering av SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) kan vara en bra informationskälla.

Antas att du som distribuerar SAP Business One-instans är:

- Bekant med att installera SAP HANA i en viss infrastruktur som en virtuell dator
- Välbekanta installerar programmet SAP Business One på en infrastruktur som virtuella Azure-datorer
- Bekant med SAP Business One och DBMS systemet valt
- Bekant med distribution av infrastruktur i Azure

Dessa områden beskrivs inte i det här dokumentet.

Förutom dokumentation om Azure bör du vara medveten om huvudsakliga SAP Notes som refererar till Business One eller som är centrala information från SAP för företag en:

- [528296 – allmän översikt över Obs för en SAP Business-versioner och relaterade produkter](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - uppdateringar dokumentet för SAP Business en 9.2, version för SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - centrala Obs för SAP Business en 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - versionen uppdaterar Obs för SAP Business en 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - kollektiva Obs för SAP Business en 9.3 allmänna problem](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - kollektiva konsult Obs för SAP HANA-Related avsnitt av SAP Business One, version för SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>En arkitektur för företag
Företag som är ett program som har två nivåer:

- En klient-nivå med en ”fat-klient
- En databasnivå med databasschemat för en klient

En bättre översikt över vilka komponenter som körs i klient-delen och vilka delar som körs i serverdelen dokumenteras i [SAP Business en Administrator's Guide](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Eftersom det inte finns vid hög fördröjning kritiska interaktion mellan den klienten och DBMS-nivå, måste båda nivåerna finnas i Azure när du distribuerar i Azure. Det är vanligt att användare sedan RDS till en eller flera virtuella datorer som kör en RDS-tjänsten för Business One klientkomponenterna.

### <a name="sizing-vms-for-sap-business-one"></a>Ändra storlek på virtuella datorer för SAP Business en

Om storleksändring av virtuella datorer-klienten resurskraven dokumenteras av SAP i dokumentet [SAP Business en maskinvara Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). För Azure måste du fokusera och beräkna med de krav som anges i kapitel 2.4 i dokumentet.

Som Azure-datorer som värd för Business One klientkomponenterna och DBMS-värden, tillåts endast virtuella datorer som är SAP NetWeaver stöds. Du hittar listan över SAP NetWeaver stöds virtuella Azure-datorer genom att läsa [SAP Obs! #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Med SAP HANA som DBMS serverdelen för ett företag endast virtuella datorer som visas för företag på HANA i den [HANA certifeid IaaS plattformslistan](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) stöds för HANA. Business One klientkomponenterna påverkas inte av den här starkare begränsningen för SAP HANA som DBMS system.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Utgåvor av operativsystem för SAP Business One

I princip är det alltid bäst att använda de senaste versionerna för operativsystemet. Särskilt i Linux-miljön infördes nya Azure-funktioner med nyare mindre versioner av Suse och Red Hat. På Windows-sida rekommenderas med hjälp av Windows Server 2016 starkt.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Distribuera infrastruktur i Azure för SAP Business One
I nästa kapitel delar den delen för att distribuera SAP i infrastrukturen.

### <a name="azure-network-infrastructure"></a>Azure nätverksinfrastruktur
Nätverkets infrastruktur du behöver distribuera i Azure beror på om du distribuerar ett enda Business One system själv. Eller om du är en värd som är värd för dussintals Business One system för kunder. Det finns också kanske små förändringar i designen på om hur du ansluter till Azure. Gå igenom olika möjligheter, en design där du har en VPN-anslutning till Azure och där du utökar Active Directory via [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) till Azure.

![Enkel nätverkskonfiguration med Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Förenklad konfiguration visas introducerar flera instanser av säkerhet som gör att kontrollen och gränsen routning. Det börjar med 

- Router/brandväggen på den lokala kunden.
- Nästa instans är den [Azure Network Security Group](https://docs.microsoft.com/azure/virtual-network/security-overview) som du kan använda för att införa regler för Routning och säkerhet för den virtuella Azure-nätverket som du kör en SAP Business-konfiguration i.
- För att undvika att användare av Business One-klienten även kan se den server som kör Business One-server som körs i databasen, måste du avgränsa den virtuella datorn som är värd för företag en klient och en företagsservern i två olika undernät inom det virtuella nätverket.
- Använder du Azure-Nätverkssäkerhetsgrupper som är tilldelade till de två olika undernäten igen för att kunna begränsa åtkomsten till den företagsservern.

En mer avancerad version av en konfiguration för Azure-nätverk baserat på Azure [beskrivs bästa praxis för arkitekturen NAV och ekrar](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). För arkitekturmönstret för NAV och ekrar ändrar första förenklad konfiguration till en så här:


![NAV och ekrar konfigurationen med verksamheten en](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

I de fall där användarna ansluter via internet utan någon privat anslutning till Azure, utformningen av nätverket i Azure bör ligga i linje med de principer som beskrivs i Azure-Referensarkitektur för [DMZ mellan Azure och Internet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>En databasserver för företag
För databastyp är SQL Server och SAP HANA tillgängliga. Oberoende av DBMS, bör du läsa dokumentet [överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) att få en allmän förståelse för DBMS distributioner i virtuella azure-datorer och den relaterade nätverk och lagring ämnen.

Även om framhållit redan i för specifika och allmänna databasdokument, bör du se själv bekant med:

- [Hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) och [hantera tillgängligheten för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SERVICEAVTAL för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

De här dokumenten bör hjälpa dig att besluta om valet av lagringstyper och konfiguration för hög tillgänglighet.

I princip bör du:

- Använd [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) över [Azure standardlagring](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- Använda Azure Managed disks över ohanterade diskar
- Se till att du har tillräckligt med IOPS och i/o-dataflöde som konfigurerats med diskkonfigurationen
- Kombinera/hana/data och /hana/log volym för att få en effektiv lagringskonfiguration för kostnad


#### <a name="sql-server-as-dbms"></a>SQLServer som DBMS
Distribuera SQL Server som DBMS för Business One, finns längs dokumentet [SQL Server Azure virtuella datorer DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Ungefärlig storleksberäkningar för DBMS-sida för SQL Server är:

| Antal användare | vCPU:er | Minne | Exempel VM-typer |
| --- | --- | --- | --- |
| upp till 20 | 4 | 16 GB | D4s_v3 E4s_v3 |
| upp till 40 | 8 | 32 GB | D8s_v3 E8s_v3 |
| upp till 80 | 16 | 64 GB | D16s_v3 E16s_v3 |
| upp till 150 | 32 | 128 GB | D32s_v3 E32s_v3 |

Den storlek som anges ovan bör ge en uppfattning om att börja med. Det kan vara att du behöver mer eller mindre resurser, vilket innebär en anpassning på azure är enkelt. En ändring mellan VM-typer är möjligt med bara en omstart av den virtuella datorn.


#### <a name="sap-hana-as-dbms"></a>SAP HANA som DBMS
Med SAP HANA som DBMS de följande avsnitten du bör följa överväganden för dokumentet [SAP HANA på Azure handboken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

För hög tillgänglighet och konfigurationer med katastrofåterställning runt SAP HANA som databas för Business One i Azure bör du läsa dokumentationen [SAP HANA, hög tillgänglighet för Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) och dokumentation visade från det dokumentet.

SAP HANA säkerhetskopierar och återställer strategier, bör du läsa dokumentet [guiden Säkerhetskopiering för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) och dokumentationen visade från det dokumentet.

 
### <a name="business-one-client-server"></a>Företag ett klient-server
Överväganden för lagring är inte det viktigaste här för dessa komponenter. dock du har en tillförlitlig plattform. Därför bör du använda Azure Premium Storage för den här virtuella datorn, även för en bas-VHD. Ändra storlek på den virtuella datorn med de data som anges i [SAP Business en maskinvara Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). För Azure måste du fokusera och beräkna med de krav som anges i kapitel 2.4 i dokumentet. När du beräknar kraven, måste du jämföra dem med följande dokument för att hitta den perfekta Virtuellt åt dig:

- [Storlekar för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP-kommentar #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Jämför antalet processorer och minne som krävs som de som beskrivs av Microsoft. Tänk också nätverkets dataflöde på när du väljer de virtuella datorerna.








