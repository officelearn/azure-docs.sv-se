---
title: SAP Business One på virtuella Azure-datorer | Microsoft-dokument
description: SAP Business One på Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101417"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One på Azure Virtual Machines
Det här dokumentet innehåller vägledning för att distribuera SAP Business One på virtuella Azure-datorer. Dokumentationen ersätter inte installationsdokumentation av Business one för SAP. Dokumentationen bör omfatta grundläggande planerings- och distributionsriktlinjer för Azure-infrastrukturen som ska köra Business One-program på.

Business One stöder två olika databaser:
- SQL Server - se [SAP Note #928839 - Versionsplanering för Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - för exakt SAP Business One-supportmatris för SAP HANA, kassan av [SAP-produkttillgänglighetsmatrisen](https://support.sap.com/pam)

När det gäller SQL Server gäller de grundläggande distributionsövervägandena som dokumenteras i [Azure Virtual Machines DBMS-distributionen för SAP NetWeaver.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) för SAP HANA nämns överväganden i detta dokument.

## <a name="prerequisites"></a>Krav
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenter:

- [Virtuella Azure-datorer i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Virtuella Azure-datorer på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Hantering av Azure-nätverk och virtuella nätverk med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Azure-nätverk och virtuella nätverk med CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Hantera Azure-diskar med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Även om du bara är intresserad av business One kan azure [virtual machines planering och implementering för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) vara en bra informationskälla.

Antagandet är att du som instans distribuerar SAP Business One är:

- Bekant med att installera SAP HANA på en viss infrastruktur som en virtuell dator
- Välbekant installation av SAP Business One-programmet på en infrastruktur som virtuella Azure-datorer
- Bekant med drift SAP Business One och DBMS-systemet valt
- Bekant med distribution av infrastruktur i Azure

Alla dessa områden kommer inte att omfattas av detta dokument.

Förutom Azure-dokumentation bör du vara medveten om de viktigaste SAP Notes, som refererar till Business One eller som är centrala anteckningar från SAP för företag Ett:

- [528296 - Allmän översiktsanteckning för SAP Business One-utgåvor och relaterade produkter](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Release Uppdateringar Anmärkning för SAP Business One 9.2, version för SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Central anmärkning för SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Release Uppdateringar Anmärkning för SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Collective Note för SAP Business One 9.3 Allmänna frågor](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Collective Consulting Note för SAP HANA-relaterade ämnen i SAP Business One, version för SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Business One-arkitektur
Business One är ett program som har två nivåer:

- En klientnivå med en "fet" klient
- En databasnivå som innehåller databasschemat för en klient

En bättre översikt över vilka komponenter som körs i klientdelen och vilka delar som körs i serverdelen dokumenteras i [SAP Business One Administrator's Guide](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Eftersom det finns en allvarlig interaktion med hög latens mellan klientnivån och DBMS-nivån måste båda nivåerna finnas i Azure när de distribueras i Azure. Det är vanligt att användarna sedan RDS till en eller flera virtuella datorer som kör en RDS-tjänst för Business One-klientkomponenterna.

### <a name="sizing-vms-for-sap-business-one"></a>Dimensionera virtuella datorer för SAP Business One

När det gäller storleken på klientens virtuella datorer dokumenteras resurskraven av SAP i dokumentet [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). För Azure måste du fokusera och beräkna med de krav som anges i kapitel 2.4 i dokumentet.

Som virtuella Azure-datorer för att vara värd för Business One-klientkomponenter och DBMS-värden tillåts endast virtuella datorer som stöds av SAP NetWeaver. Om du vill hitta listan över virtuella SAP NetWeaver-datorer som stöds av Azure läser du [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Köra SAP HANA som DBMS backend för Business One, endast virtuella datorer, som är listade för företag på HANA i [HANA certifeid IaaS plattformslista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) stöds för HANA. Business One-klientkomponenterna påverkas inte av den här starkare begränsningen för SAP HANA som DBMS-system.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Operativsystemversioner som ska användas för SAP Business One

I princip är det alltid bäst att använda de senaste operativsystemutgåvorna. Speciellt i Linux-utrymmet introducerades nya Azure-funktioner med olika nyare mindre versioner av Suse och Red Hat. På Windows-sidan rekommenderas du starkt att använda Windows Server 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Distribuera infrastruktur i Azure för SAP Business One
I de närmaste kapitlen, den infrastruktur bitar som är viktiga för att distribuera SAP.

### <a name="azure-network-infrastructure"></a>Azure-nätverksinfrastruktur
Vilken nätverksinfrastruktur du behöver distribuera i Azure beror på om du distribuerar ett enda Business One-system själv. Eller om du är en värdare som är värd för dussintals Business One-system för kunder. Det kan också finnas små ändringar i designen om hur du ansluter till Azure. Gå igenom olika möjligheter, en design där du har en VPN-anslutning till Azure och där du utökar din Active Directory via [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) till Azure.

![Enkel nätverkskonfiguration med Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Den förenklade konfigurationen som presenteras introducerar flera säkerhetsinstanser som gör det möjligt att styra och begränsa routning. Det börjar med 

- Routern/brandväggen på kundens lokala sida.
- Nästa instans är [Azure Network Security Group](https://docs.microsoft.com/azure/virtual-network/security-overview) som du kan använda för att införa routnings- och säkerhetsregler för Azure VNet som du kör din SAP Business-konfiguration i.
- För att undvika att användare av Business One-klienten också kan se servern som kör Business One-servern, som kör databasen, bör du separera den virtuella datorn som är värd för Business one-klienten och företaget en server i två olika undernät inom det virtuella nätverket.
- Du skulle använda Azure NSG som tilldelats de två olika undernäten igen för att begränsa åtkomsten till Business one-servern.

En mer sofistikerad version av en Azure-nätverkskonfiguration baseras på de [Azure-dokumenterade metodtipsen för hubb- och ekerarkitektur](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Arkitekturmönstret för nav och eker skulle ändra den första förenklade konfigurationen till en så här:


![Hubb- och ekerkonfiguration med Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

I de fall där användarna ansluter via internet utan privat anslutning till Azure, bör utformningen av nätverket i Azure anpassas till de principer som dokumenteras i Azure-referensarkitekturen för [DMZ mellan Azure och Internet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Business One-databasserver
För databastypen är SQL Server och SAP HANA tillgängliga. Oberoende av DBMS bör du läsa dokumentet [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) att få en allmän förståelse av DBMS-distributioner i virtuella azure-datorer och relaterade nätverks- och lagringsavsnitt.

Även om du redan har betonat i de specifika och allmänna databasdokumenten bör du bekanta dig med:

- [Hantera tillgängligheten för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) och [hantera tillgängligheten för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SLA för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Dessa dokument bör hjälpa dig att bestämma valet av lagringstyper och konfiguration med hög tillgänglighet.

I princip bör du:

- Använd Premium SSD-enheter via vanliga hårddiskar. Mer information om tillgängliga disktyper finns i vår artikel [Välj en disktyp](../../windows/disks-types.md)
- Använda Azure Managed-diskar över ohanterade diskar
- Kontrollera att du har tillräckligt med IOPS- och I/O-dataflöde konfigurerat med diskkonfigurationen
- Kombinera /hana/data och /hana/log volym för att ha en kostnadseffektiv lagringskonfiguration


#### <a name="sql-server-as-dbms"></a>SQL Server som DBMS
För distribution av SQL Server som DBMS för Business One går du längs dokumentet [SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Grova storleksuppskattningar för DBMS-sidan för SQL Server är:

| Antal användare | Virtuella processorer | Minne | Exempel på vm-typer |
| --- | --- | --- | --- |
| upp till 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| upp till 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| upp till 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| upp till 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Den storlek som anges ovan bör ge en uppfattning om var man ska börja med. Det kan vara så att du behöver mindre eller mer resurser, i vilket fall en anpassning på azure är lätt. En ändring mellan vm-typer är möjlig med bara en omstart av den virtuella datorn.

#### <a name="sap-hana-as-dbms"></a>SAP HANA som DBMS
Använda SAP HANA som DBMS följande avsnitt bör du följa övervägandena i dokumentet [SAP HANA på Azure operations guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

För konfigurationer med hög tillgänglighet och haveriberedskap runt SAP HANA som databas för Business One i Azure bör du läsa dokumentationen [SAP HANA hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) och dokumentationen som pekar på från det dokumentet.

För SAP HANA-strategier för säkerhetskopiering och återställning av SAP bör du läsa [dokumentets säkerhetskopieringsguide för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) och dokumentationen som pekades på från det dokumentet.

 
### <a name="business-one-client-server"></a>Business One-klientserver
För dessa komponenter lagring överväganden är inte det primära problemet. Ändå vill du ha en pålitlig plattform. Därför bör du använda Azure Premium Storage för den här virtuella datorn, även för bas-VHD. Storleksändring av den virtuella datorn, med data som anges i [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). För Azure måste du fokusera och beräkna med de krav som anges i kapitel 2.4 i dokumentet. När du beräknar kraven måste du jämföra dem med följande dokument för att hitta den perfekta virtuella datorn för dig:

- [Storlekar för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP-#1928533](https://launchpad.support.sap.com/#/notes/1928533)

Jämför antalet processorer och minne som behövs med det som dokumenteras av Microsoft. Tänk också på nätverksdataflödet när du väljer de virtuella datorerna.








