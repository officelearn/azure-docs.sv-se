---
title: Konfigurera IBM Db2 HADR på Azure virtual machines (VM) | Microsoft Docs
description: Skapa hög tillgänglighet för IBM Db2 LUW på Azure virtual machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689713"
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Hög tillgänglighet för IBM Db2 LUW på Azure Virtual Machines på SUSE Linux Enterprise Server med Pacemaker

IBM Db2 för Linux, UNIX- och Windows (LUW) i [hög tillgänglighet och katastrofåterställning (HADR) återställningskonfiguration](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) består av en nod som kör en instans av primära databasen och minst en nod som kör en sekundär databas-instans. Ändringar av den primära databasinstansen replikeras till en sekundär databasinstans synkront eller asynkront, beroende på din konfiguration. 

Den här artikeln beskriver hur du distribuerar och konfigurerar Azure-datorer (VM), installera kluster framework och installera IBM Db2 LUW med HADR konfiguration. 

Artikeln beskriver inte hur du installerar och konfigurerar IBM Db2 LUW med HADR eller SAP Programvaruinstallation. För att utföra dessa uppgifter ska tillhandahåller vi referenser till SAP och IBM-installation-handböcker. Den här artikeln handlar om delar som är specifika för Azure-miljön. 

IBM Db2-versioner som stöds är 10,5 och senare, enligt beskrivningen i SAP-kommentar [1928533].

Innan du påbörjar en installation, se följande SAP-information och dokumentation:

| SAP-kommentar | Beskrivning |
| --- | --- |
| [1928533] | SAP-program i Azure: Produkter som stöds och Azure VM-typer |
| [2015553] | SAP på Azure: Supportkrav |
| [2178632] | Nyckeln som övervakning av mått för SAP på Azure |
| [2191498] | SAP på Linux med Azure: Förbättrad övervakning |
| [2243692] | Linux på Azure (IaaS) virtuell dator: Problem med SAP-licens |
| [1984787] | SUSE LINUX Enterprise Server 12: Installationsinformation |
| [1999351] | Felsökning av förbättrad Azure övervakning för SAP |
| [2233094] | DB6: SAP-program på Azure som använder IBM Db2 för Linux, UNIX- och Windows - ytterligare information |
| [1612105] | DB6: Vanliga frågor och svar på Db2 med HADR |


| Dokumentation | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Har alla nödvändiga SAP Notes för Linux |
| [Azure virtuella datorer, planering och implementering av SAP på Linux] [ planning-guide] guide |
| [Azure Virtual Machines-distribution för SAP på Linux] [ deployment-guide] (den här artikeln) |
| [Azure-datorer databasdistribution management system(DBMS) för SAP på Linux] [ dbms-guide] guide |
| [SAP-arbetsbelastningar på Azure checklista för planering och distribution][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server för SAP-program 12 SP3 bästa guider med metodtips][sles-for-sap-bp] |
| [SUSE Linux Enterprise hög tillgänglighet tillägget 12 SP3][sles-ha-guide] |
| [IBM Db2 Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet, IBM Db2 LUW med HADR är installerad på minst två Azure-datorer, som är distribuerade i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) eller över [Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

På följande bild visar en konfiguration för två databasserver virtuella Azure-datorer. Både databasservern virtuella Azure-datorer har sina egna lagringsutrymme som är anslutet och är igång och körs. I HADR har en databasinstans i någon av virtuella Azure-datorer rollen för den primära instansen. Alla klienter är anslutna till den här primära-instansen. Alla ändringar i databastransaktioner sparas lokalt i transaktionsloggen Db2. Eftersom transaktionen loggposter sparas lokalt överförs posterna via TCP/IP till databasinstansen på andra databasservern, standby-server eller standby-instansen. Väntelägesinstansen uppdaterar den lokala databasen genom att återställa framåt överförda transaktionen loggposter. På så sätt kan hålls reservservern synkroniserade med den primära servern.

HADR är bara en replikering funktionalitet. Det har inga felidentifiering och inga automatiska övertagande eller växla över resurser. Ett övertagande av eller överföring till standby-servern måste initieras manuellt av en databasadministratör. Du kan använda Linux Pacemaker klusterfunktionen för att uppnå en automatisk ta över och felsökning. Pacemaker övervakar två database server-instanser. När den primära instansen går sönder Pacemaker initierar en *automatisk* HADR övertagande av standby-servern. Pacemaker innebär också att den virtuella IP-adressen tilldelas till den nya primära servern.

![Översikt över hög tillgänglighet för IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Ha SAP-programservrar ansluta till den primära databasen, behöver du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av redundans ansluter SAP-programservrarna till den nya primära databasinstansen. I en Azure-miljö, en [Azure-belastningsutjämnare](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) krävs för att använda en virtuell IP-adress på sätt som krävs för HADR IBM Db2. 

Följande bild visar en översikt över en högtillgänglig installation av ett SAP-system baserat på IBM Db2-databas så att du förstår hur IBM Db2 LUW med HADR och Pacemaker passar in i en högtillgänglig installation av SAP-system. Den här artikeln beskriver bara IBM Db2, men den innehåller referenser till andra artiklar om hur du ställer in andra komponenter i ett SAP-system.

![IBM DB2 översikt över hela miljön i hög tillgänglighet](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Översikt över steg som krävs
Om du vill distribuera en IBM Db2-konfiguration, måste du följa dessa steg:

  + Planera din miljö.
  + Distribuera de virtuella datorerna.
  + Uppdatera SUSE Linux och konfigurera filsystem.
  + Installera och konfigurera Pacemaker.
  + Installera [med hög tillgänglighet NFS][nfs-ha].
  + Installera [ASCS/ÄNDARE på ett separat kluster][ascs-ha].
  + Installera IBM Db2-databas med alternativ för distribuerade/hög tillgänglighet (SWPM).
  + Installera och skapa en sekundär databasnod och instans och konfigurera HADR.
  + Bekräfta att HADR fungerar.
  + Avser kontroll IBM Db2 Pacemaker konfigurationen.
  + Konfigurera Azure-belastningsutjämnare.
  + Installera primära och dialogrutan programservrar.
  + Kontrollera och anpassa konfigurationen av SAP-programservrar.
  + Utför redundans och gäller tester.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planera Azure-infrastrukturen som värd för IBM Db2 LUW med HADR

Slutför planeringsprocessen innan du utför distributionen. Planera skapar grunden för att distribuera en konfiguration av Db2 med HADR i Azure. Viktiga element som måste vara en del i att planera för Interimistiska Db2 LUW (databasen del av SAP-miljön) visas i följande tabell:

| Avsnitt | Kort beskrivning |
| --- | --- |
| Definiera Azure-resursgrupper | Resursgrupper där du distribuerar virtuella datorer, virtuella nätverk, Azure Load Balancer och andra resurser. Kan vara befintliga eller nya. |
| Virtuellt nätverk / undernätsdefinition | Där virtuella datorer för IBM Db2 och Azure Load Balancer som distribueras. Kan vara befintliga eller nya. |
| Virtuella datorer som är värd för IBM Db2 LUW | VM-storlek, lagring, nätverk, IP-adress. |
| Virtuella värdnamn och virtuella IP-Adressen för IBM Db2-databas| Det virtuella IP- eller namnet som används för anslutning av SAP-programservrar. **DB-virt-värdnamnet**, **db-virt-IP-**. |
| Att hägna in Azure | Att hägna in Azure eller uppstår hägna in (rekommenderas). Metod för att undvika delad hjärna situationer förhindras. |
| SBD VM | Uppstår VM-storlek, lagring, nätverk. |
| Azure Load Balancer | Användning av Basic eller Standard (rekommenderas), avsökningsport för Db2-databas (vår rekommendation 62500) **avsökningsporten**. |
| Namnmatchning| Hur namnmatchningen fungerar i miljön. DNS-tjänsten rekommenderas starkt. Lokala värdfil som kan användas. |
    
Mer information om Linux Pacemaker i Azure finns i [konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Distribution på SUSE Linux

Resurs-agenten för IBM Db2 LUW ingår i SUSE Linux Enterprise Server för SAP-program. För installation som beskrivs i det här dokumentet, måste du använda SUSE Linux-Server för SAP-program. Azure Marketplace innehåller en bild för SUSE Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella Azure-datorer. Tänk på de olika modellerna support eller tjänst som erbjuds av SUSE via Azure Marketplace när du väljer en VM-avbildning i Azure Marketplace för virtuella datorer. 

### <a name="hosts-dns-updates"></a>Värdar: DNS-uppdateringar
Se en lista över alla värdnamn, inklusive virtuella värdnamn och uppdatera DNS-servrarna för att aktivera rätt IP-adress till värd-namnmatchning. Om en DNS-server inte finns eller du kan inte uppdatera och skapa DNS-poster kan behöva du använda lokal värd-filer för de enskilda virtuella datorer som deltar i det här scenariot. Om du använder värdposter för filer, se till att posterna tillämpas på alla virtuella datorer i miljön för SAP-system. Vi rekommenderar dock att du använder din DNS-server som helst utökar till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrollera att den valda OS stöds av IBM/SAP för IBM Db2 LUW. Lista över operativsystemversioner som stöds för virtuella Azure-datorer och Db2 versioner finns i SAP-kommentar [1928533]. Listan över versioner av enskilda Db2 version är tillgänglig i matrisen SAP produkten tillgänglighet. Vi rekommenderar minst SLES 12 SP3 på grund av Azure-relaterade prestandaförbättringar i det här eller senare SUSE Linux-versioner.

1. Skapa eller välj en resursgrupp.
1. Skapa eller välj ett virtuellt nätverk och undernät.
1. Skapa en Azure-tillgänglighetsuppsättning eller distribuera en tillgänglighetszon.
    + Ange de maximala uppdateringsdomäner för tillgänglighetsuppsättning, till 2.
1. Skapa virtuell dator 1.
    + Använd SLES för SAP-avbildning i Azure Marketplace.
    + Välj den Azure-tillgänglighetsuppsättning som du skapade i steg 3, eller Tillgänglighetszon.
1.  Skapa virtuell dator 2.
    + Använd SLES för SAP-avbildning i Azure Marketplace.
    + Välj den Azure-tillgänglighetsuppsättning du i skapade i steg 3 eller välja Tillgänglighetszon (inte i samma zon som i steg 3).
1. Lägga till datadiskar till de virtuella datorerna och sedan kontrollera rekommendation av en fil systeminställningarna i artikeln [IBM Db2 Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Skapa Pacemaker-kluster
    
För att skapa en grundläggande Pacemaker kluster för den här IBM Db2-servern, se [konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installera IBM Db2 LUW och SAP-miljön

Innan du påbörjar installationen av en SAP-miljö baserat på IBM Db2 LUW, granska följande dokumentation:

+ Dokumentation om Azure
+ SAP-dokumentationen
+ IBM-dokumentation

Länkar till den här dokumentationen tillhandahålls i inledningen av den här artikeln.

Kontrollera handböcker för SAP-installation om hur du installerar NetWeaver-baserade program på IBM Db2 LUW.

Du kan hitta guider på att SAP-portalen med hjälp av den [SAP Installation Guide Finder][sap-instfind].

Du kan minska antalet guider som visas i portalen genom att ange följande filter:

- Jag vill: ”Installera ett nytt system”
- Min databas: ”IBM Db2 för Linux, Unix- och Windows”
- Ytterligare filter för SAP NetWeaver-versioner, konfiguration eller operativsystem

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationen tips för att konfigurera IBM Db2 LUW med HADR

Du ställer in den primära IBM Db2 LUW-databasinstansen:

- Använd alternativet hög tillgänglighet eller distribuerade.
- Installera SAP ASCS/ÄNDARE och Database-instansen.
- Ta en säkerhetskopia av den nyligen installerade databasen.


> [!IMPORTANT] 
> Anteckna ”databas kommunikationsporten” som anges under installationen. Det måste vara samma portnummer för båda databasinstanser

Om du vill konfigurera databasservern vänteläge med hjälp av SAP homogena system kopia proceduren, kör du dessa steg:

1. Välj den **System copy** alternativet > **rikta system** > **distribuerade** > **databasinstansen**.
1. Som en kopieringsmetod för att, Välj **homogena System** så att du kan använda Säkerhetskopiering för att återställa en säkerhetskopia på reservserverinstansen.
1. När du når det avsluta steget för att återställa databasen för homogena system kopia avsluta installationsprogrammet. Återställ databasen från en säkerhetskopia av den primära värddatorn. Alla efterföljande installation faser har redan körts på den primära databas-servern.
1. Ställ in HADR för IBM Db2.

   > [!NOTE]
   > För installation och konfiguration som är specifik för Azure och Pacemaker: Det finns en explicit fråga om hög tillgänglighet för IBM Db2 LUW under installationsproceduren via SAP Software etablering Manager:
   >+ Markera inte **IBM Db2 pureScale**.
   >+ Markera inte **installera IBM Tivoli automatisering i System för Multiplatforms**.
   >+ Markera inte **generera konfigurationsfiler för kluster**.

   När du använder en uppstår enhet för Linux Pacemaker kan du ange följande Db2 HADR parametrar:
   + HADR peer fönstret varaktighet (sekunder) (HADR_PEER_WINDOW) = 300  
   + HADR timeout-värdet (HADR_TIMEOUT) = 60

   När du använder en Azure-Pacemaker hägna in agent kan du ange följande parametrar:
   + HADR peer fönstret varaktighet (sekunder) (HADR_PEER_WINDOW) = 900  
   + HADR timeout-värdet (HADR_TIMEOUT) = 60

Vi rekommenderar att parametrarna föregående baserat på första redundans/övertagande av testet. Det är obligatoriskt att du testar för redundans och ta över fungera korrekt med de här parameterinställningarna. Eftersom enskilda konfigurationer kan variera, kräva parametrarna för justering. 

> [!IMPORTANT]
> Specifika till IBM Db2 med HADR konfiguration med normal start: Den sekundära eller standby-database-instansen måste vara igång innan du kan starta den primära databas-instansen.

För exempelsyfte och de förfaranden som beskrivs i den här artikeln kan databasen SID är **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR kontroll
När du har konfigurerat HADR och statusen är PEER- och ansluten på primär och vänteläge noderna, kontrollera följande:

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



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker konfiguration

När du använder Pacemaker för automatisk redundans i händelse av ett nodfel, måste du konfigurera din Db2-instanser och Pacemaker enligt detta. Det här avsnittet beskriver den här typen av konfiguration.

Följande objekt har ett prefix med antingen:

- **[A]**: Gäller för alla noder
- **[1]**: Gäller endast för nod 1 
- **[2]**: Gäller endast för nod 2

**[A]**  Krav för Pacemaker konfiguration:
1. Stäng av båda databasservrar med användaren db2\<sid > med db2stop.
1. Ändra gränssnittsmiljön för db2\<sid > användaren */bin/ksh*. Vi rekommenderar att du använder verktyget Yast. 


### <a name="pacemaker-configuration"></a>Pacemaker konfiguration

**[1]**  IBM Db2 HADR-specifika Pacemaker konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Skapa IBM Db2-resurser:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Starta IBM Db2-resurser:
* Placera Pacemaker från underhållsläget.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Kontrollera att klusterstatusen är OK och att alla resurser har startats. Det är inte viktigt vilken nod som resurserna som körs på.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 noder som har konfigurerats
# <a name="5-resources-configured"></a>5 resurser som har konfigurerats

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Fullständig lista över resurser:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith uppstår (stonith:external / uppstår): Igång azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Resursgrupp: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Igång azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: något):      Igång azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Master/Slave-uppsättning: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Huvud: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Slaves: [azibmdb01]
</pre>

> [!IMPORTANT]
> Du måste hantera Pacemaker klustrad Db2-instans med hjälp av Pacemaker verktyg. Om du använder db2-kommandon, till exempel db2stop identifieras Pacemaker åtgärden som ett fel för resursen. Om du utför underhåll, kan du placera noder eller resurser i underhållsläge. Pacemaker pausar övervakning resurser och du kan sedan använda normala db2 administration kommandon.


### <a name="configure-azure-load-balancer"></a>Konfigurera Azure Load Balancer
För att konfigurera Azure Load Balancer, rekommenderar vi att du använder den [Azure Standard Load Balancer-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) och sedan gör du följande:

1. Skapa en IP-adresspool på klientsidan:

   a. Öppna Azure Load Balancer i Azure-portalen, Välj **IP-adresspool på klientdelen**, och välj sedan **Lägg till**.

   b. Ange namnet på den nya IP-adresspoolen på klientsidan (till exempel **Db2-connection**).

   c. Ange den **tilldelning** till **statiska**, och ange IP-adressen **virtuella IP-** definierats i början.

   d. Välj **OK**.

   e. När den nya IP-adresspoolen på klientsidan har skapats kan du notera pool IP-adressen.

1. Skapa en backend pool:

   a. Öppna Azure Load Balancer i Azure-portalen, Välj **serverdelspooler**, och välj sedan **Lägg till**.

   b. Ange namnet på den nya backend-poolen (till exempel **Db2-backend**).

   c. Välj **lägga till en virtuell dator**.

   d. Välj tillgänglighetsuppsättning eller de virtuella datorerna som är värd för IBM Db2-databas som skapats i föregående steg.

   e. Välj de virtuella datorerna i IBM Db2-klustret.

   f. Välj **OK**.

1. Skapa en hälsoavsökning:

   a. Öppna Azure Load Balancer i Azure-portalen, Välj **hälsoavsökningar**, och välj **Lägg till**.

   b. Ange namnet på den nya hälsoavsökningen (till exempel **Db2 hp**).

   c. Välj **TCP** som protokoll och port **62500**. Behåll den **intervall** värdet **5**, och hålla den **tröskelvärde för ej felfri** värdet **2**.

   d. Välj **OK**.

1. Skapa regler för belastningsutjämning:

   a. Öppna Azure Load Balancer i Azure-portalen, Välj **belastningsutjämningsregler**, och välj sedan **Lägg till**.

   b. Ange namnet på den nya regeln för Load Balancer (till exempel **Db2-SID**).

   c. Välj den frontend IP-adressen, backend poolen och hälsoavsökningen som du skapade tidigare (till exempel **Db2-klientdel**).

   d. Behåll den **protokollet** inställd **TCP**, och ange port *databasen kommunikationsporten*.

   e. Öka den **timeout för inaktivitet** till 30 minuter.

   f. Se till att **aktivera flytande IP**.

   g. Välj **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Gör ändringar i SAP-profiler som ska användas virtuell IP-adress för anslutningen
Programnivån måste använda den virtuella IP-adressen som du definierat och konfigurerats för Azure Load Balancer för att ansluta till den primära instansen av HADR-konfigurationen av SAP. Följande ändringar krävs:

/sapmnt/\<SID >/profil/standard. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installera primära och dialogrutan programservrar

När du installerar en primär och dialogrutan programservrar mot en Db2-HADR-konfiguration, Använd den virtuella värden namn som valt du för konfigurationen. 

Om du har utfört installationen innan du skapade Db2 HADR-konfiguration, göra ändringarna som beskrivs i föregående avsnitt och på följande sätt för SAP-Java-stackar.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Kontrollera ABAP + Java eller Java stack-system JDBC-Webbadressen

Verktyget J2EE Config används för att kontrollera eller uppdatera JDBC-Webbadressen. Eftersom J2EE Config-verktyget är ett grafiskt verktyg, måste du ha X server som är installerad:
 
1. Logga in på den primära servern för J2EE-instansen och kör:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. I det vänstra fönstret, väljer **security store**.
1. I den högra rutan, väljer du den viktiga jdbc/poolen / \ <SAPSID> /URL.
1. Ändra värdnamnet i JDBC-Webbadressen till det virtuella värdnamnet.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Välj **lägga till**.
1. Välj diskikonen för i det övre vänstra hörnet för att spara ändringarna.
1. Stäng konfigurationsverktyget.
1. Java-instansen startas om.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurera log arkivering av HADR installationen
Om du vill konfigurera Db2 loggen arkivering för HADR installation, rekommenderar vi att du konfigurerar både primär och vänteläge databasen har funktionen för automatisk hämtning från alla platser för log-Arkiv. Både primär och vänteläge databasen måste kunna hämta arkivera loggfiler från alla log Arkiv platser du vilken antingen av databasen instanser kan arkivera loggfiler. 

Arkivering av loggen utförs bara av den primära databasen. Om du ändrar HADR roller med database-servrar, eller om ett fel inträffar, ansvarar den nya primära databasen för arkivering av loggen. Om du har konfigurerat flera log Arkiv platser, kan dina loggar arkiveras två gånger. I händelse av en lokal eller fjärransluten inspelad kan du också behöva manuellt kopiera arkiverade loggar från den gamla primära servern till den aktiva platsen för den nya primära servern.

Vi rekommenderar att du konfigurerar en gemensam NFS-resurs där loggarna skrivs från båda noderna. NFS-resursen måste vara med hög tillgänglighet. 

Du kan använda befintliga med hög tillgänglighet NFS-resurser för transporter eller en profilkatalog. Mer information finns i:

- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][nfs-ha] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (för att skapa NFS-resurser)


## <a name="test-the-cluster-setup"></a>Testa konfiguration

Det här avsnittet beskrivs hur du kan testa din Db2 HADR-konfiguration. *Varje test förutsätter att du är inloggad som användarrot* och den primära IBM Db2-databasen körs på den *azibmdb01* virtuell dator.

Den första statusen för alla testfall beskrivs här: (crm_mon - r eller crm status)

- **status för CRM** är en ögonblicksbild av status för Pacemaker vid tiden för körningen 
- **crm_mon - r** är kontinuerlig utdata från Pacemaker status

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktionen DBACOCKPIT > konfiguration > Översikt, enligt följande bild:

![DBACockpit - före migrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testa övertagande av IBM Db2


> [!IMPORTANT] 
> Kontrollera följande innan du startar testet:
> * Pacemaker har inte alla misslyckade åtgärder (crm-status).
> * Det finns inga Platsbegränsningar (rester av migreringen test)
> * IBM Db2 HADR synkroniseringen fungerar. Kontrollera med användaren db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrera den nod som kör den primära Db2-databasen genom att köra följande kommando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

När migreringen är klar, crm-status-utdata som ser ut som:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktionen DBACOCKPIT > konfiguration > Översikt, enligt följande bild:

![DBACockpit - Postmigrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Resursmigrering med ”crm resource migrera” skapar Platsbegränsningar. Platsbegränsningar bör tas bort. Om Platsbegränsningar inte raderas resursen kan inte återställa eller så kan du uppleva oönskad övertag. 

Migrera resursen till *azibmdb01* och avmarkera Platsbegränsningar
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **CRM-resurs migrera \<res_name > <host>:** Skapar Platsbegränsningar och kan orsaka problem med ett övertagande av
- **CRM-resurs som är tydligt \<res_name >**: Tar bort Platsbegränsningar
- **rensning av resurser för CRM \<res_name >**: Tar bort alla fel för resursen

### <a name="test-the-fencing-agent"></a>Testa att hägna in agenten

I det här fallet kan vi testa uppstår hägna in som vi rekommenderar att du gör när du använder SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Klusternod *azibmdb01* ska startas om. IBM Db2 primära HADR rollen ska flyttas till *azibmdb02*. När *azibmdb01* är tillbaka online, Db2 instansen kommer att flytta i rollen för en sekundär databas-instans. 

Om tjänsten Pacemaker inte startar automatiskt på den primära omstartad tidigare, måste du starta den manuellt med:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testa övertagande av ett manuell

Du kan testa övertagande av ett manuellt genom att stoppa tjänsten Pacemaker på *azibmdb01* nod:
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Efter redundansen kan du kan starta tjänsten igen på *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Avsluta Db2-processen på den nod som kör den primära databasen HADR

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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker startar om den primära Db2-databasinstansen på samma nod eller den växlar över till den nod som kör den sekundära databas-instansen och ett fel rapporteras.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Avsluta Db2-processen på den nod som kör sekundär databas-instans

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Noden hämtar into misslyckades angivna och fel rapporteras
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2-instans hämtar startas om i den sekundära rollen som den hade tilldelats innan.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop kraft på den nod som kör HADR primära databasinstans

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Som användare db2\<sid > köra kommandot db2stop kraft:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Enhetsfel har identifierats
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Den sekundära databasinstansen för Db2 HADR har befordras till den primära rollen
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Krascha VM med omstart på den nod som kör HADR primära databasinstans

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker flyttar upp den sekundära instansen till rollen primära-instansen. Den gamla primära instansen flyttas till den sekundära rollen när den virtuella datorn och alla tjänster som är helt återställas när den virtuella datorn startas om:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära HADR-databasinstansen med ”stopp”

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

I sådana fall identifierar Pacemaker att den nod som kör den primära databas-instansen inte svarar.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Nästa steg är att söka efter en *dela hjärna* situation. När den kvarvarande noden har fastställt att den nod som sista körde den primära databas-instansen inte är tillgänglig, körs en redundansväxling av resurser.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


I händelse av en ”stoppa” på noden har den felaktiga noden startas om via Azure-hanteringsverktyg (i Azure portal, PowerShell eller Azure CLI). När den felaktiga noden är online igen, startar Db2-instans i den sekundära rollen.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Nästa steg
- [Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

