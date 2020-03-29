---
title: Konfigurera IBM Db2 HADR på virtuella Azure-datorer (VIRTUELLA datorer) | Microsoft-dokument
description: Upprätta hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer (VIRTUELLA datorer).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926745"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med Pacemaker

IBM Db2 för Linux, UNIX och Windows (LUW) i [hadr-konfiguration (High Availability and disaster recovery)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) består av en nod som kör en primär databasinstans och minst en nod som kör en sekundär databasinstans. Ändringar i den primära databasinstansen replikeras till en sekundär databasinstans synkront eller asynkront, beroende på din konfiguration. 

I den här artikeln beskrivs hur du distribuerar och konfigurerar virtuella Azure-datorer ( Virtuella datorer), installerar klusterramverket och installerar IBM Db2 LUW med HADR-konfiguration. 

Artikeln täcker inte hur du installerar och konfigurerar IBM Db2 LUW med HADR- eller SAP-programinstallation. För att hjälpa dig att utföra dessa uppgifter tillhandahåller vi referenser till SAP- och IBM-installationshandböcker. Den här artikeln fokuserar på delar som är specifika för Azure-miljön. 

De IBM Db2-versioner som stöds är 10,5 och senare, vilket dokumenteras i SAP note [1928533].

Innan du påbörjar en installation läser du följande SAP-anteckningar och dokumentation:

| SAP-anteckning | Beskrivning |
| --- | --- |
| [1928533] | SAP-program på Azure: Produkter som stöds och Azure VM-typer |
| [2015553] | SAP på Azure: Supportkrav |
| [2178632] | Viktiga övervakningsmått för SAP på Azure |
| [2191498] | SAP på Linux med Azure: Förbättrad övervakning |
| [2243692] | Linux på Azure (IaaS) VM: SAP-licensproblem |
| [1984787] | SUSE LINUX Enterprise Server 12: Installationsanteckningar |
| [1999351] | Felsöka förbättrad Azure-övervakning för SAP |
| [2233094] | DB6: SAP-program på Azure som använder IBM Db2 för Linux, UNIX och Windows – ytterligare information |
| [1612105] | DB6: Vanliga frågor om Db2 med HADR |


| Dokumentation | 
| --- |
| [SAP Community Wiki:](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)Har alla nödvändiga SAP Notes för Linux |
| [Planering och implementering av Virtuella Azure-datorer för SAP på Linux-guide][planning-guide] |
| [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide] (den här artikeln) |
| [Azure Virtual Machines databashanteringssystem (DBMS) distribution för SAP på Linux-guide][dbms-guide] |
| [SAP-arbetsbelastning på Azure-planerings- och distributionschecklista][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server för SAP-program 12 SP4 bästa praxis guider][sles-for-sap-bp] |
| [SUSE Linux Enterprise förlängning med hög tillgänglighet 12 SP4][sles-ha-guide] |
| [DBMS-distribution för SAP-arbetsbelastning för IBM Db2 på virtuella Azure-datorer][dbms-db2] |
| [IBM Db2 HADR 11,1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet installeras IBM Db2 LUW med HADR på minst två virtuella Azure-datorer som distribueras i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) eller över [Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Följande grafik visar en installation av två virtuella Azure-datorer för databasservern. Båda databasservern Azure virtuella datorer har sin egen lagring ansluten och är igång. I HADR har en databasinstans i en av Azure virtuella datorer rollen som primär instans. Alla klienter är anslutna till den här primära instansen. Alla ändringar i databastransaktioner sparas lokalt i db2-transaktionsloggen. När transaktionsloggposterna sparas lokalt överförs posterna via TCP/IP till databasinstansen på den andra databasservern, vänteservern eller standby-instansen. Väntelägesinstansen uppdaterar den lokala databasen genom att överföra de överförda transaktionsloggposterna framåt. På så sätt synkroniseras standby-servern med den primära servern.

HADR är bara en replikeringsfunktion. Den har ingen feldetektering och inga automatiska uppköps- eller redundansanläggningar. Ett övertagande eller en överföring till vänteservern måste initieras manuellt av en databasadministratör. För att uppnå en automatisk uppköps- och felidentifiering kan du använda Klustringsfunktionen för Linux Pacemaker. Pacemaker övervakar de två databasserverinstanserna. När den primära databasserverinstansen kraschar initierar Pacemaker ett *automatiskt* HADR-övertagande av standby-servern. Pacemaker ser också till att den virtuella IP-adressen tilldelas den nya primära servern.

![Översikt över IBM Db2 med hög tillgänglighet](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Om du vill att SAP-programservrar ska ansluta till primär databas behöver du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av en redundans ansluter SAP-programservrarna till en ny primär databasinstans. I en Azure-miljö krävs en [Azure-belastningsutjämnare](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) för att använda en virtuell IP-adress på det sätt som krävs för HADR för IBM Db2. 

För att hjälpa dig att till fullo förstå hur IBM Db2 LUW med HADR och Pacemaker passar in i en högtillgänglig SAP-systeminstallation, presenterar följande avbildning en översikt över en mycket tillgänglig installation av ett SAP-system baserat på IBM Db2-databas. Den här artikeln täcker endast IBM Db2, men den innehåller referenser till andra artiklar om hur du ställer in andra komponenter i ett SAP-system.

![IBM DB2 hög tillgänglighet full miljö översikt](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Översikt på hög nivå över de steg som krävs
Om du vill distribuera en IBM Db2-konfiguration måste du följa dessa steg:

  + Planera din miljö.
  + Distribuera de virtuella datorerna.
  + Uppdatera SUSE Linux och konfigurera filsystem.
  + Installera och konfigurera Pacemaker.
  + Installera [NFS med högtillgänge][nfs-ha].
  + Installera [ASCS/ERS i ett separat kluster][ascs-ha].
  + Installera IBM Db2-databas med alternativet Distribuerad/hög tillgänglighet (SWPM).
  + Installera och skapa en sekundär databasnod och instans och konfigurera HADR.
  + Bekräfta att HADR fungerar.
  + Använd Pacemaker-konfigurationen för att styra IBM Db2.
  + Konfigurera Azure Load Balancer.
  + Installera primära programservrar och dialogprogramservrar.
  + Kontrollera och anpassa konfigurationen av SAP-programservrar.
  + Utför redundans- och uppköpstester.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planera Azure-infrastruktur för hosting IBM Db2 LUW med HADR

Slutför planeringsprocessen innan du kör distributionen. Planering bygger grunden för distribution av en konfiguration av Db2 med HADR i Azure. Viktiga element som måste ingå i planeringen för IMB Db2 LUW (databasdel av SAP-miljö) visas i följande tabell:

| Hjälpavsnitt | Kort beskrivning |
| --- | --- |
| Definiera Azure-resursgrupper | Resursgrupper där du distribuerar VM, VNet, Azure Load Balancer och andra resurser. Kan vara befintlig eller ny. |
| Definition av virtuellt nätverk / undernät | Där virtuella datorer för IBM Db2 och Azure Load Balancer distribueras. Kan vara befintliga eller nyskapade. |
| Virtuella maskiner som är värdar för IBM Db2 LUW | VM-storlek, lagring, nätverk, IP-adress. |
| Virtuellt värdnamn och virtuell IP för IBM Db2-databas| Det virtuella IP- eller värdnamnet som används för anslutning av SAP-programservrar. **db-virt-hostname**, **db-virt-ip**. |
| Azure stängsel | Azure fäktning eller SBD stängsel (rekommenderas starkt). Metod för att undvika delade hjärnsituationer. |
| Virtuell SBD-dator | SBD virtuell dator storlek, lagring, nätverk. |
| Azure Load Balancer | Användning av Basic eller Standard (rekommenderas), sondport för Db2-databas (vår rekommendation 62500) **probe-port**. |
| Namnmatchning| Hur namnmatchning fungerar i miljön. DNS-tjänsten rekommenderas starkt. Lokal hosts-fil kan användas. |
    
Mer information om Linux Pacemaker i Azure finns i [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Distribution på SUSE Linux

Resursagenten för IBM Db2 LUW ingår i SUSE Linux Enterprise Server för SAP-program. För den konfiguration som beskrivs i det här dokumentet måste du använda SUSE Linux Server för SAP-program. Azure Marketplace innehåller en avbildning för SUSE Enterprise Server för SAP Applications 12 som du kan använda för att distribuera nya virtuella Azure-datorer. Var medveten om de olika support- eller tjänstmodeller som erbjuds av SUSE via Azure Marketplace när du väljer en VM-avbildning på Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Värdar: DNS-uppdateringar
Gör en lista över alla värdnamn, inklusive virtuella värdnamn, och uppdatera DNS-servrarna så att korrekt IP-adress kan vara värdnamnsmatchning. Om det inte finns en DNS-server eller om du inte kan uppdatera och skapa DNS-poster måste du använda de lokala värdfilerna för de enskilda virtuella datorer som deltar i det här scenariot. Om du använder värdfilposter kontrollerar du att posterna tillämpas på alla virtuella datorer i SAP-systemmiljön. Vi rekommenderar dock att du använder din DNS som helst sträcker sig till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrollera att det valda operativsystemet stöds av IBM/SAP för IBM Db2 LUW. Listan över operativsystemversioner som stöds för Virtuella Azure-datorer och Db2-versioner finns i SAP note [1928533]. Listan över OS-utgåvor av enskilda Db2-versioner finns i SAP-produkttillgänglighetsmatrisen. Vi rekommenderar starkt ett minimum av SLES 12 SP4 på grund av Azure-relaterade prestandaförbättringar i den här eller senare SUSE Linux-versionerna.

1. Skapa eller välj en resursgrupp.
1. Skapa eller välj ett virtuellt nätverk och undernät.
1. Skapa en Azure-tillgänglighetsuppsättning eller distribuera en tillgänglighetszon.
    + För tillgänglighetsuppsättningen anger du de maximala uppdateringsdomänerna till 2.
1. Skapa virtuell dator 1.
    + Använd SLES för SAP-avbildning på Azure Marketplace.
    + Välj den Azure-tillgänglighetsuppsättning som du skapade i steg 3 eller välj Tillgänglighetszon.
1.  Skapa virtuell dator 2.
    + Använd SLES för SAP-avbildning på Azure Marketplace.
    + Välj azure-tillgänglighetsuppsättningen som du har skapat i steg 3 eller välj Tillgänglighetszon (inte samma zon som i steg 3).
1. Lägg till datadiskar till de virtuella datorerna och kontrollera sedan rekommendationen för en filsystemkonfiguration i artikeln [IBM Db2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Skapa pacemakerklustret
    
Information om hur du skapar ett grundläggande Pacemaker-kluster för den här IBM Db2-servern finns i [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installera IBM Db2 LUW- och SAP-miljön

Innan du startar installationen av en SAP-miljö baserat på IBM Db2 LUW läser du följande dokumentation:

+ Azure-dokumentation
+ SAP-dokumentation
+ IBM-dokumentation

Länkar till den här dokumentationen finns i det inledande avsnittet i den här artikeln.

Kontrollera SAP-installationshandböckerna om hur du installerar NetWeaver-baserade program på IBM Db2 LUW.

Du hittar guiderna på SAP:s hjälpportal med hjälp av [SAP-installationsguiden Finder][sap-instfind].

Du kan minska antalet stödlinjer som visas i portalen genom att ange följande filter:

- Jag vill: "Installera ett nytt system"
- Min databas: "IBM Db2 för Linux, Unix och Windows"
- Ytterligare filter för SAP NetWeaver-versioner, stackkonfiguration eller operativsystem

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationstips för att ställa in IBM Db2 LUW med HADR

Så här konfigurerar du den primära IBM Db2 LUW-databasinstansen:

- Använd alternativet hög tillgänglighet eller distribuerad.
- Installera SAP ASCS/ERS och Database-instansen.
- Gör en säkerhetskopia av den nyinstallerade databasen.


> [!IMPORTANT] 
> Skriv ner "Databaskommunikationsporten" som är inställd under installationen. Det måste vara samma portnummer för båda databasinstanserna

Så här konfigurerar du databasen för vänteläge med hjälp av sap-homogena systemkopieringsproceduren:

1. Välj alternativet **Systemkopiering** > **inriktningssystem** > **distribuerad** > **databasinstans**.
1. Som kopieringsmetod väljer du **Homogent system** så att du kan använda säkerhetskopian för att återställa en säkerhetskopia på serverinstansen för vänteläge.
1. När du når avslutningssteget för att återställa databasen för homogen systemkopia avslutar du installationsprogrammet. Återställ databasen från en säkerhetskopia av den primära värden. Alla efterföljande installationsfaser har redan körts på den primära databasservern.
1. Ställ in HADR för IBM Db2.

   > [!NOTE]
   > För installation och konfiguration som är specifik för Azure och Pacemaker: Under installationsproceduren via SAP Software Provisioning Manager finns det en tydlig fråga om hög tillgänglighet för IBM Db2 LUW:
   >+ Välj inte **IBM Db2 pureScale**.
   >+ Välj inte **Installera IBM Tivoli System Automation för multiplattformar**.
   >+ Välj inte **Generera klusterkonfigurationsfiler**.

   När du använder en SBD-enhet för Linux Pacemaker ställer du in följande Db2 HADR-parametrar:
   + HADR-peer-fönstrets varaktighet (sekunder) (HADR_PEER_WINDOW) = 300  
   + TIMEOUT-värde för HADR (HADR_TIMEOUT) = 60

   När du använder en Azure Pacemaker-stängselagent ställer du in följande parametrar:
   + HADR-peer-fönstrets varaktighet (sekunder) (HADR_PEER_WINDOW) = 900  
   + TIMEOUT-värde för HADR (HADR_TIMEOUT) = 60

Vi rekommenderar föregående parametrar baserat på inledande redundans-/uppköpstestning. Det är obligatoriskt att du testar för korrekt funktionalitet vid redundans och uppköp med dessa parameterinställningar. Eftersom enskilda konfigurationer kan variera kan parametrarna kräva justering. 

> [!IMPORTANT]
> Specifik för IBM Db2 med HADR-konfiguration med normal start: Den sekundära eller standby-databasinstansen måste vara igång innan du kan starta den primära databasinstansen.

För demonstrationsändamål och de förfaranden som beskrivs i denna artikel är databasen SID **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR kontroll
När du har konfigurerat HADR och statusen är PEER och ANSLUTEN på de primära noderna och standby-noderna utför du följande kontroll:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker-konfiguration

När du använder Pacemaker för automatisk redundans i händelse av ett nodfel måste du konfigurera db2-instanserna och pacemakern därefter. I det här avsnittet beskrivs den här typen av konfiguration.

Följande objekt föregås av antingen:

- **[A]**: Gäller för alla noder
- **[1]**: Gäller endast för nod 1 
- **[2]**: Gäller endast för nod 2

**[A]** Förutsättningar för Pacemaker-konfiguration:
1. Stäng av båda databasservrarna\<med användare db2 sid> med db2stop.
1. Ändra skalmiljön för\<db2 sid> användare till */bin/ksh*. Vi rekommenderar att du använder Yast-verktyget. 


### <a name="pacemaker-configuration"></a>Pacemaker-konfiguration

> [!IMPORTANT]
> Nyligen genomförda tester visade situationer, där netcat slutar svara på förfrågningar på grund av eftersläpning och dess begränsning av hantering endast en anslutning. Netcat-resursen slutar lyssna på Azure Load Balancer-begäranden och den flytande IP-adressen blir otillgänglig.  
> För befintliga Pacemaker-kluster rekommenderade vi tidigare att ersätta netcat med socat. För närvarande rekommenderar vi att du använder azure-lb-resursagent, som är en del av paketets resursagenter, med följande paketversionskrav:
> - För SLES 12 SP4/SP5 måste versionen vara minst resursagenter-4.3.018.a7fb5035-3.30.1.  
> - För SLES 15/15 SP1 måste versionen vara minst resursagenter-4.3.0184.6ee15eb2-4.13.1.  
>
> Observera att ändringen kräver korta driftstopp.  
> För befintliga Pacemaker-kluster, om konfigurationen redan har ändrats för att använda socat enligt beskrivningen i [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), finns det inget krav på att växla omedelbart till azure-lb-resursagenten.

**[1]** IBM Db2 HADR-specifik Pacemaker-konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Skapa IBM Db2-resurser:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Starta IBM Db2-resurser:
* Lägg pacemakern ur underhållsläge.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Kontrollera att klusterstatusen är OK och att alla resurser har startats. Det är inte viktigt vilken nod resurserna körs på.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 noder konfigurerade
# <a name="5-resources-configured"></a>5 konfigurerade resurser

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Fullständig lista över resurser:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Startade azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resursgrupp: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Startade azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Startade azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Huvud-/slavuppsättning: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mästare: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slavar: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Du måste hantera pacemakerhopad Db2-instans med hjälp av Pacemaker-verktyg. Om du använder db2-kommandon som db2stop identifierar Pacemaker åtgärden som ett resursfel. Om du utför underhåll kan du placera noderna eller resurserna i underhållsläge. Pacemaker avbryter övervakningsresurser och du kan sedan använda vanliga db2-administrationskommandon.


### <a name="configure-azure-load-balancer"></a>Konfigurera Azure Load Balancer
Om du vill konfigurera Azure Load Balancer rekommenderar vi att du använder [SKU för Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) och sedan gör följande;

> [!NOTE]
> Standardbelastningsutjämnaren SKU har begränsningar för åtkomst till offentliga IP-adresser från noderna under belastningsutjämnaren. Artikeln [Offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) med hög tillgänglighet beskriver hur du aktiverar dessa noder för att komma åt offentliga IP-adresser

1. Skapa en IP-pool i frontend:

   a. Öppna Azure Load Balancer i Azure-portalen, välj **IP-pool med klientdel**och välj sedan **Lägg till**.

   b. Ange namnet på den nya ip-poolen för frontend (till exempel **Db2-anslutning).**

   c. Ange **tilldelningen** till **Statisk**och ange den **virtuella IP-adressen som** definierats i början.

   d. Välj **OK**.

   e. När den nya ip-poolen i fronten har skapats noterar du poolens IP-adress.

1. Skapa en backend-pool:

   a. Öppna Azure Load Balancer i Azure-portalen, välj **bakåtsträvningar**och välj sedan **Lägg till**.

   b. Ange namnet på den nya backend-poolen (till exempel **Db2-backend**).

   c. Välj **Lägg till en virtuell dator**.

   d. Välj tillgänglighetsuppsättningen eller de virtuella datorer som är värdar för IBM Db2-databasen som skapats i föregående steg.

   e. Välj virtuella datorer i IBM Db2-klustret.

   f. Välj **OK**.

1. Skapa en hälsoavsökning:

   a. Öppna Azure Load Balancer i Azure-portalen, välj **hälsoavsökningar**och välj **Lägg till**.

   b. Ange namnet på den nya hälsoavsökningen (till exempel **Db2-hp**).

   c. Välj **TCP** som protokoll och port **62500**. Behåll **intervallvärdet** inställt **på 5**och behåll det **felaktiga tröskelvärdet** inställt på **2**.

   d. Välj **OK**.

1. Skapa belastningsutjämningsregler:

   a. Öppna Azure Load Balancer i Azure-portalen, välj **Belastningsutjämningsregler**och välj sedan **Lägg till**.

   b. Ange namnet på den nya belastningsutjämnarens regel (till exempel **Db2-SID**).

   c. Välj ip-adressen för klientdelen, backend-poolen och hälsoavsökningen som du skapade tidigare (till exempel **Db2-frontend**).

   d. Behåll **protokollet** inställt på **TCP**och ange port *Port Communication Port*.

   e. Öka **tidsgränsen** för inaktiv tid till 30 minuter.

   f. Se till att **aktivera Flytande IP**.

   g. Välj **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Gör ändringar i SAP-profiler för att använda virtuell IP för anslutning
För att ansluta till den primära instansen av HADR-konfigurationen måste SAP-programlagret använda den virtuella IP-adressen som du definierade och konfigurerade för Azure Load Balancer. Följande ändringar krävs:

/sapmnt/\<SID>/profil/DEFAULT. PFL (PFL)
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installera primära programservrar och dialogprogramservrar

När du installerar primära program- och dialogprogramservrar mot en Db2 HADR-konfiguration använder du det virtuella värdnamnet som du valde för konfigurationen. 

Om du utförde installationen innan du skapade Db2 HADR-konfigurationen gör du ändringarna enligt beskrivningen i föregående avsnitt och enligt följande för SAP Java-stackar.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java eller Java stack system JDBC URL kontrollera

Använd verktyget J2EE Config för att kontrollera eller uppdatera JDBC-URL:en. Eftersom J2EE Config-verktyget är ett grafiskt verktyg måste du ha X-servern installerad:
 
1. Logga in på den primära programservern för J2EE-instansen och kör:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Välj **säkerhetsarkivet**i den vänstra ramen .
1. I den högra ramen väljer du nyckeln\<jdbc/pool/ SAPSID>/url.
1. Ändra värdnamnet i JDBC-URL:en till det virtuella värdnamnet.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Välj **Lägg till**.
1. Om du vill spara ändringarna väljer du diskikonen längst upp till vänster.
1. Stäng konfigurationsverktyget.
1. Starta om Java-instansen.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurera loggarkivering för HADR-installation
Om du vill konfigurera Db2-loggarkiveringen för HADR-installationen rekommenderar vi att du konfigurerar både den primära och den standby-databasen så att den har automatisk logghämtningsfunktion från alla loggarkivplatser. Både den primära databasen och standby-databasen måste kunna hämta loggarkivfiler från alla loggarkivplatser som någon av databasinstanserna kan arkivera loggfiler till. 

Loggarkiveringen utförs endast av den primära databasen. Om du ändrar DATABASSERVRARNAS HADR-roller eller om ett fel inträffar, ansvarar den nya primära databasen för loggarkivering. Om du har konfigurerat flera loggarkivplatser kan dina loggar arkiveras två gånger. I händelse av en lokal eller fjärrinfångst kan du också behöva kopiera de arkiverade loggarna manuellt från den gamla primära servern till den aktiva loggplatsen för den nya primära servern.

Vi rekommenderar att du konfigurerar en gemensam NFS-resurs där loggar skrivs från båda noderna. NFS-aktien måste vara mycket tillgänglig. 

Du kan använda befintliga NFS-resurser med högtillgänge för transporter eller en profilkatalog. Mer information finns i:

- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][nfs-ha] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (för att skapa NFS-resurser)


## <a name="test-the-cluster-setup"></a>Testa klusterinställningarna

I det här avsnittet beskrivs hur du kan testa din Db2 HADR-konfiguration. *Varje test förutsätter att du är inloggad som användarrot* och IBM Db2-primärt körs på den virtuella *azibmdb01-datorn.*

Den ursprungliga statusen för alla testfall förklaras här: (crm_mon -r- eller crm-status)

- **crm-status** är en ögonblicksbild av Pacemaker-status vid körning 
- **crm_mon -r** är kontinuerlig utgång av Pacemaker-status

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i Transaction DBACOCKPIT > Configuration > Overview, som visas i följande bild:

![DBACockpit - Före migrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Test övertagande av IBM Db2


> [!IMPORTANT] 
> Innan du startar testet ska du se till att:
> * Pacemaker har inga misslyckade åtgärder (crm-status).
> * Det finns inga platsbegränsningar (rester av migreringstest)
> * IBM Db2 HADR-synkroniseringen fungerar. Kontrollera med användaren\<db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrera noden som kör den primära Db2-databasen genom att köra följande kommando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

När migreringen är klar ser crm-statusutdata ut så här:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i Transaction DBACOCKPIT > Configuration > Overview, som visas i följande bild:

![DBACockpit - Post Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Resursmigrering med "crm-resursmigrera" skapar platsbegränsningar. Platsbegränsningar bör tas bort. Om platsbegränsningar inte tas bort kan resursen inte återställas eller så kan du uppleva oönskade uppköp. 

Migrera resursen tillbaka till *azibmdb01* och rensa platsbegränsningarna
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm-resurs \<migrera res_name> \<värd>:** Skapar platsbegränsningar och kan orsaka problem med uppköp
- **crm-resurs \<tydlig res_name>**: Rensar platsbegränsningar
- **crm-resurs \<rensning res_name>: **Rensar alla fel i resursen

### <a name="test-the-fencing-agent"></a>Testa fäktningsmedlet

I det här fallet testar vi SBD-stängsel, vilket vi rekommenderar att du gör när du använder SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Klusternod *azibmdb01* bör startas om. IBM Db2 primära HADR roll kommer att flyttas till *azibmdb02*. När *azibmdb01* är online igen kommer Db2-instansen att flytta i rollen som en sekundär databasinstans. 

Om Pacemaker-tjänsten inte startar automatiskt på den omstartade tidigare primärt ska du börja den manuellt med:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testa ett manuellt övertagande

Du kan testa ett manuellt uppköp genom att stoppa Pacemaker-tjänsten på *azibmdb01-noden:*
<pre><code>service pacemaker stop</code></pre>

status på *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Efter redundansen kan du starta tjänsten igen på *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Döda Db2-processen på noden som kör HADR-primärdatabasen

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2-instansen kommer att misslyckas och Pacemaker rapporterar följande status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker startar om db2-primärdatabasinstansen på samma nod, eller så växlar den över till den nod som kör den sekundära databasinstansen och ett fel rapporteras.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Döda Db2-processen på noden som kör den sekundära databasinstansen

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Noden hamnar i felaktigt angivet och felrapporterat
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2-instansen startas om i den sekundära rollen som den hade tilldelat tidigare.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop-kraft på noden som kör den primära HADR-databasinstansen

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Som användare\<db2 sid> utföra kommandot db2stop kraft:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Fel upptäcktes
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR-sekundär databasinstansen befordrades till den primära rollen
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Krasch virtuell dator med omstart på noden som kör HADR primära databasinstans

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker befordrar den sekundära instansen till rollen primär instans. Den gamla primära instansen flyttas till den sekundära rollen efter den virtuella datorn och alla tjänster återställs helt efter omstarten av den virtuella datorn:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära HADR-databasinstansen med "stopp"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

I så fall upptäcker Pacemaker att noden som kör den primära databasinstansen inte svarar.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Nästa steg är att leta efter en *delad hjärnsituation.* När den efterlevande noden har fastställt att noden som senast körde den primära databasinstansen är nere, körs en redundans av resurser.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


I händelse av en "stoppa" nod måste den misslyckade noden startas om via Azure Management-verktyg (i Azure-portalen, PowerShell eller Azure CLI). När den misslyckade noden är online igen startar den Db2-instansen i den sekundära rollen.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Nästa steg
- [Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

