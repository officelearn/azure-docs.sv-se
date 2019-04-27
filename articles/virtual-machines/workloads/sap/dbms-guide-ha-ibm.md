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
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835981"
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

IBM Db2 LUW (Linux, Unix- och Windows) i [HADR configuration](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) består av en nod som kör en instans av primära databasen och minst en nod som kör en sekundär databas-instans. Ändringarna till den primära databasinstansen komma replikeras till sekundär databasinstansen synkront eller asynkront, beroende på din konfiguration. 

Den här artikeln beskriver hur du distribuerar och konfigurerar de virtuella datorerna, installera kluster-ramverket och installera och konfigurera IBM Db2 LUW i HADR konfiguration. Artikeln förklarar inte hur du installerar och konfigurerar IBM Db2 LUW i HADR eller SAP Programvaruinstallation. Referenser till SAP och IBM installationen handböcker som utför dessa uppgifter. Fokus ligger på delar som är specifika för Azure-miljön. 

IBM Db2-versioner som stöds är 10,5 och högre enligt beskrivningen i SAP Obs! #[1928533].

Läs följande SAP notes och dokumentationen innan du närmar sig en installation:

| SAP-kommentar | Beskrivning |
| --- | --- |
| [1928533] | SAP-program i Azure: Produkter som stöds och Azure VM-typer |
| [2015553] | SAP på Microsoft Azure: Supportkrav |
| [2178632] | Nyckeln som övervakning av mått för SAP på Microsoft Azure |
| [2191498] | SAP på Linux med Azure: Förbättrad övervakning |
| [2243692] | Linux på Microsoft Azure (IaaS) virtuell dator: Problem med SAP-licens |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationsinformation |
| [1999351] |Felsökning av utökad Azure övervakning för SAP |
| [2233094] |DB6: SAP-program i Azure med IBM Db2 för Linux, UNIX- och Windows - ytterligare Information |
| [1612105] |DB6: Vanliga frågor och svar på hög tillgänglighet för Db2-katastrofåterställning (HADR) |


| Dokumentation | 
| --- |
| [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux |
| [Azure virtuella datorer, planering och implementering av SAP på Linux] [ planning-guide] guide |
| [Azure Virtual Machines-distribution för SAP på Linux] [ deployment-guide] (den här artikeln) |
| [Azure Virtual Machines DBMS-distribution för SAP på Linux] [ dbms-guide] guide |
| [SAP-arbetsbelastningar på Azure checklista för planering och distribution][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server för SAP-program 12 SP3 bästa guider med metodtips][sles-for-sap-bp] |
| [SUSE Linux Enterprise hög tillgänglighet tillägget 12 SP3][sles-ha-guide] |
| [IBM Db2 Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar][dbms-db2] |
| [Haveriberedskap för IBM Db2 hög tillgänglighet 11.1][db2-hadr-11.1] |
| [Haveriberedskap för IBM Db2 hög tillgänglighet R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet, IBM Db2 LUW med HADR är installerad på minst två Azure-datorer, som är distribuerade i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) eller över [Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). Bilderna nedan visar en konfiguration av databasserver för två virtuella datorer i Azure. Både databasservern virtuella Azure-datorer har sina egna lagringsutrymme som är anslutet och är igång och körs. I HADR har en databasinstans i någon av virtuella Azure-datorer rollen för den primära instansen. Alla klienter är anslutna till den här primära-instansen. Alla ändringar i databastransaktioner sparas lokalt i transaktionsloggen Db2. Eftersom transaktionen loggposter sparas lokalt överförs posterna via TCP/IP till databasinstansen på andra databasservern, standby-server eller standby-instansen. Väntelägesinstansen uppdaterar den lokala databasen genom att återställa framåt överförda transaktionen loggposter. Så hålls reservservern synkroniserade med den primära servern.

HADR är bara en replikering funktionalitet. Det har inga felidentifiering och inga automatiska övertagande eller växla över resurser. Ett övertagande av eller överföra Nollställda måste initieras manuellt av en databasadministratör. Du kan använda Linux Pacemaker funktionen kluster för att uppnå en automatisk ta över och felsökning. Pacemaker övervakar två servrar/databasinstanser. När den primära databasen server/instansen kraschar, Pacemaker initierar en **automatisk** HADR övertagande av standby-servern och säkerställer också att den virtuella IP-adressen tilldelas till den nya primära servern.

![Översikt över IBM Db2 hög tillgänglighet](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

För SAP behöver programservrar att ansluta till den primära databasen som du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av redundans ansluter SAP-programservrarna till den nya primära databasinstansen. I en Azure-miljö, en [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) krävs för att använda en virtuell IP-adress på sätt som krävs för HADR IBM Db2. 

Följande bild visar en översikt över en högtillgänglig installation av ett SAP-system baserat på IBM Db2-databas för att helt förstå hur IBM Db2 LUW med HADR och Pacemaker passar in i en högtillgänglig SAP systeminställningarna. Den här artikeln täcker endast IBM Db2 och referenser till andra artiklar om hur du ställer in andra komponenter i SAP-system.

![IBM DB2 HA fullständig översikt över miljön](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Översikt över steg som krävs
För att distribuera en IBM Db2-konfiguration, måste de här stegen för att omfattas:

  + Planera-miljön
  + Distribuera virtuella datorer
  + Uppdatera SUSE Linux och konfigurera filsystem
  + Installera och konfigurera Pacemaker
  + Installera [med hög tillgänglighet för NFS][nfs-ha]
  + Installera [ASCS/ÄNDARE på separata kluster][ascs-ha] 
  + Installera IBM Db2-databas med alternativ för distribuerade/hög tillgänglighet (SWPM)
  + Installation/skapa sekundär databasnod och instans och konfigurera HADR
  + Bekräfta att HADR fungerar
  + Tillämpa konfigurationen för Pacemaker till kontroll IBM Db2
  + Konfigurera Azure Load Balancer 
  + Installera primära + dialogrutan programservrar
  + Kontrollera/anpassa konfigurationen för SAP-programservrar
  + Utför redundans / ta över testar



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planera Azure-infrastrukturen som värd för IBM Db2 LUW med HADR

Gå igenom känsligt innan du utför distributionen. Det är att skapa grunden för att distribuera en konfiguration av Db2 med HADR i Azure. Viktiga element som måste vara en del i att planera för Interimistiska Db2 LUW (databasen del av SAP-miljön).

| Avsnitt | Kort beskrivning |
| --- | --- |
| Definiera Azure resursgrupperna | Resursgrupperna där du distribuerar virtuella datorer, virtuella nätverk, Azure Load Balancer och andra resurser. Kan vara befintliga eller nya |
| Virtuellt nätverk / undernätsdefinition | Där virtuella datorer för IBM Db2 och Azure Load Balancer får distribueras. Kan vara befintliga eller nya |
| Virtuella datorer som är värd för IBM Db2 LUW | VM-storlek, lagring, nätverk, IP-adress |
| Virtuella värdnamn & virtuella IP-Adressen för IBM Db2-databas| Virtuell IP/värdnamn som används för anslutning av SAP-programservrar. **DB-virt-värdnamnet**, **db-virt-ip** |
| Att hägna in Azure | Att hägna in Azure eller uppstår hägna in (rekommenderas). Metod för att undvika delad hjärna situationer förhindras |
| SBD VM | Uppstår virtuell datorstorlek, lagring, nätverk |
| Azure Load Balancer | Användning av Basic eller Standard (rekommenderas), avsökningsport för Db2-databas (vår rekommendation 62500) **avsökningsporten** |
| Namnmatchning| Hur namnmatchningen fungerar i miljön. DNS-tjänsten rekommenderas starkt. Lokala värdfil som kan användas |
    
Mer information om användningen av Linux Pacemaker i Azure finns i följande artiklar:

- [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Distribution på SUSE Linux

Resurs-agenten för IBM Db2 LUW ingår i SUSE Linux Enterprise Server för SAP-program. För den inställning som beskrivs i det här dokumentet, är det obligatoriskt att använda SUSE Linux-Server för SAP-program. Azure Marketplace innehåller en bild för SUSE Enterprise Server för SAP-program 12 som du kan använda för att distribuera nya virtuella Azure-datorer. Tänk på de olika supporttjänster modellerna som erbjuds av SUSE via Azure gallerier när du väljer den Virtuella datoravbildningen i Virtuella Azure-galleriet. 

### <a name="hosts---dns-updates"></a>-Värdar – DNS-uppdateringar
Skapa en lista över alla värdnamn, inklusive virtuella värdnamn och uppdatera DNS-servrarna för att aktivera rätt IP-adress till värdnamnsmatchning. I fallet kan en DNS-server finns inte eller det går inte att uppdatera och skapa DNS-poster, måste du använda lokala värdar filer för enskilda virtuella datorer som deltar i det här scenariot. Om använder värdposter för filer, måste du kontrollera att posterna tillämpas på alla virtuella datorer i miljön för SAP-system. Dock är rekommendation att använda din DNS-server som helst utökas till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrollera att den valda OS stöds av IBM/SAP för IBM Db2 LUW. Lista över operativsystemversioner som stöds för virtuella Azure-datorer och Db2 versioner finns i SAP-kommentar [1928533]. Listan över versioner av enskilda Db2-versioner finns i matrisen SAP produkten tillgänglighet. Vi rekommenderar minst SLES 12 SP3 på grund av Azure relaterade prestandaförbättringar i det här eller senare SUSE Linux-versioner.

1. Skapa/välja en resursgrupp
2. Skapa/välja ett virtuellt nätverk och undernät
3. Skapa Azure-tillgänglighetsuppsättning eller distribuera i Tillgänglighetszon
    + Tillgänglighet konfigurera – Ställ in max uppdateringsdomäner till två
4. Skapa virtuell dator 1.
    + Använda SLES för SAP-avbildning i Azure-galleriet
    + Välj Azure tillgänglighetsuppsättning i skapade i steg 3 eller välj Tillgänglighetszon
5.  Skapa virtuell dator 2.
    + Använda SLES för SAP-avbildning i Azure-galleriet
    + Välj Azure tillgänglighetsuppsättning i skapades i steg 3. eller välj Tillgänglighetszon - inte samma zon som i steg 3.
6. Lägga till datadiskar till de virtuella datorerna - Kontrollera rekommendation av filsystem-konfiguration i artikeln [IBM Db2 Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Skapa Pacemaker-kluster
    
Följ stegen i [konfigurerar Pacemaker på SUSE Linux Enterprise Server i Azure][sles-pacemaker] att skapa ett grundläggande Pacemaker kluster för den här IBM Db2-servern. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Installera IBM Db2 LUW och SAP-miljön

Innan du startar installationen av en SAP-miljö baserat på IBM Db2 LUW granska (länkar i början av artikeln):

+ Dokumentation om Azure
+ SAP-dokumentationen
+ IBM-dokumentation

Kontrollera installationen handböcker från SAP på hur du installerar NetWeaver-baserade program på IBM Db2 LUW.

Du kan hitta guider på SAP hjälpa portal med den [SAP Installation Guide Finder][sap-instfind]

Du kan filtrera sökningen så att minska antalet guider som är tillgängliga med att ange filter:

+ Jag vill: ”Installera ett nytt system”
+ Min databas: ”IBM Db2 för Linux, Unix- och Windows”
+ Ytterligare filter för SAP Netweaver-versioner, konfiguration eller operativsystem.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationen tips för att konfigurera IBM Db2 LUW med HADR

Ställ in den primära IBM Db2 LUW-databasinstansen:

- Använda hög tillgänglighet eller distribuerade alternativ
- Installera SAP ASCS/ÄNDARE och Database-instans
- Ta en säkerhetskopia av nyinstallerade databas


> [!IMPORTANT] 
> Anteckna ”databas kommunikationsporten” under installationen. Det måste vara samma portnummer för båda databasinstanser

För att konfigurera databasservern vänteläge med SAP: s homogena system kopia proceduren, kör du dessa steg:

  - Använd alternativet för System-kopia – målinstans för system - distribuerad - databasen.
  - Välj homogena System Copy som kopieringsmetod för att, så att du kan använda Säkerhetskopiering för att återställa en säkerhetskopia på vänteläge server-instansen
  - När du når det avsluta steget för att återställa databasen för homogena system kopia avsluta installationsprogrammet. Återställ databasen från en säkerhetskopia av den primära värddatorn. Alla efterföljande installation faser har redan körts på den primära databas-servern
- Konfigurera HADR för IBM Db2

> [!NOTE]
> Installation/konfiguration som är specifika för Azure och Pacemaker. Det finns en explicit fråga om hög tillgänglighet för IBM Db2 LUW under installationsproceduren via SAP Software etablering Manager:
>+ Markera inte IBM Db2 pureScale
>+ Markera inte ”installera IBM Tivoli System Automation för Multiplatforms
>+ Markera inte ”generera kluster Adressreferens filer”

> [!NOTE]
>När du använder en uppstår enhet för Linux Pacemaker ange Db2 HADR-parametrar
>+ HADR peer fönstret varaktighet (sekunder) (HADR_PEER_WINDOW) = 300  
>+ HADR timeout-värdet (HADR_TIMEOUT) = 60

> [!NOTE]
>Med Azure Pacemaker hägna in agent:
>+ HADR peer fönstret varaktighet (sekunder) (HADR_PEER_WINDOW) = 900  
>+ HADR timeout-värdet (HADR_TIMEOUT) = 60

Parametrar rekommenderas utifrån första redundans/övertagande av testet. Det är obligatoriskt att testa ta över och redundans med de här parameterinställningarna fungera korrekt. Eftersom enskilda konfigurationer kan variera, kräva justering med dessa parametrar. 

> [!IMPORTANT]
> Specifika för IBM Db2 i HADR konfiguration med normal start - sekundär/vänteläge-databasinstans måste vara igång innan du kan starta primära databasinstansen.

Demonstrationssyfte och metoden som beskrivs i det här dokumentet finns databasen SID är **PTR**.

##### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR kontroll
När du har konfigurerat HADR och status är PEER- och ansluten på noderna primär och vänteläge.

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

När du använder Pacemaker för automatisk redundans vid ett nodfel, måste du konfigurera din Db2-instanser och Pacemaker enligt detta. Det här avsnittet beskriver den här typen av konfiguration.

Följande objekt har ett prefix med antingen:

- **[A]**  – gäller för alla noder
- **[1]**  – gäller endast för nod 1 
- **[2]**  – gäller endast för nod 2.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Krav för Pacemaker konfiguration:
1. Stäng av båda databasserver med användaren db2\<sid > med db2stop
2. Ändra shell-miljön för db2\<sid > användaren att ”/ bin/ksh” - rekommenderar att du använder verktyget Yast 


### <a name="pacemaker-configuration"></a>Pacemaker konfiguration:

**[1]**  IBM Db2 HADR specifik Pacemaker konfiguration
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Skapa IBM Db2-resurser
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
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

**[1]**  Starta IBM Db2-resurser – placera Pacemaker underhållsläge
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Kontrollera att klusterstatusen är ok och att alla resurser har startats. Det är inte viktigt i vilken nod som resurserna som körs.
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
> Hantera Pacemaker klustrad Db2-instans måste göras via Pacemaker verktyg. Db2-kommandona (till exempel db2stop) identifieras av Pacemaker som felet för resursen. Du kan placera noder eller resurser i underhållsläge vid underhåll, och Pacemaker ska göra uppehåll i övervakningen resurser och normala db2 administration kommandon kan användas.


### <a name="configure-azure-load-balancer"></a>Konfigurera Azure Load Balancer
Det rekommenderas att använda den [Azure Standard Load Balancer-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Konfigurera Azure Load Balancer (via Azure portal). Börja med att skapa en IP-adresspool på klientsidan:

   1. Öppna Azure Load Balancer i Azure-portalen, Välj **IP-adresspool på klientdelen**, och välj **Lägg till**.
   2. Ange namnet på den nya IP-adresspoolen på klientsidan (till exempel **Db2-connection**).
   3. Ange den **tilldelning** till **statiska** och ange IP-adressen **virtuella IP-** definierats i början.
   4. Välj **OK**.
   5. När den nya IP-adresspoolen på klientsidan har skapats kan du notera pool IP-adressen.

2. Nästa steg är att skapa en backend-pool:

   1. Öppna Azure Load Balancer i Azure-portalen, Välj **serverdelspooler**, och välj **Lägg till**.
   2. Ange namnet på den nya backend-poolen (till exempel **Db2-backend**).
   3. Välj **lägga till en virtuell dator**.
   4. Välj de tillgänglighet set/virtuella datorer som är värd för IBM Db2-databas som skapades i steg 3.
   5. Välj de virtuella datorerna i IBM Db2-klustret.
   6. Välj **OK**.

3. Tredje steget är att skapa en hälsoavsökning:

   1. Öppna Azure Load Balancer i Azure-portalen, Välj **hälsoavsökningar**, och välj **Lägg till**.
   2. Ange namnet på den nya hälsoavsökningen (till exempel **Db2 hp**).
   3. Välj **TCP** som protokoll och port **62500**. Behåll den **intervall** värdet 5, och **tröskelvärde för ej felfri** värdet 2.
   4. Välj **OK**.

4. Skapa regler för belastningsutjämning:

   1. Öppna Azure Load Balancer i Azure-portalen, Välj **belastningsutjämningsregler**, och välj **Lägg till**.
   2. Ange namnet på den nya regeln för Load Balancer (till exempel **Db2-SID**).
   3. Välj den frontend IP-adressen, backend poolen och hälsoavsökningen som du skapade tidigare (till exempel **Db2-klientdel**).
   4. Behåll den **protokollet** inställd **TCP**, och ange port *databasen kommunikationsporten*.
   5. Öka den **timeout för inaktivitet** till 30 minuter.
   6. Se till att **aktivera flytande IP**.
   7. Välj **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Gör ändringar i SAP-profiler som ska användas virtuell IP-adress för anslutningen
SAP-programnivån behöver använda den virtuella IP-adressen du definierat och konfigurerats för Azure Load Balancer att ansluta till den primära instansen av HADR-konfigurationen. Följande ändringar krävs.

/sapmnt/\<SID >/profil/standard. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installera primära och dialogrutan programmet servrar

När installerar primära och dialogrutan programservrar mot en Db2-HADR-konfiguration, bör du använda virtuella värdnamn som du valt för konfigurationen. 

Om du har utfört installationen innan du skapar Db2 HADR-konfigurationen, måste du göra ändringar som beskrivs i föregående stycke och på följande sätt för SAP-Java-stackar.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Kontrollera ABAP + Java eller Java stack-system JDBC-Webbadressen

Verktyget J2EE Config används för att kontrollera eller uppdatera JDBC-Webbadressen. Den J2EE Config-verktyget är grafiska verktyg, därför måste **X server** installerad:
 
1. Logga in på den primära servern för J2EE-instansen och kör:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. I det vänstra fönstret, väljer du security store.
2. I den högra rutan, väljer du den viktigajdbc/poolen/ <SAPSID> /URL.
2. Ändra värdnamnet i JDBC-Webbadressen till det virtuella värdnamnet <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Välj Lägg till.
5. Klicka på diskikonen i det övre vänstra hörnet för att spara ändringarna.
5. Stäng konfigurationsverktyget.
5. Java-instansen startas om.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Konfigurationen av loggen arkivering för HADR installation
Om du vill konfigurera Db2 loggen arkivering för HADR installation, rekommenderar vi att du konfigurerar både primär och vänteläge databasen har funktionen för automatisk hämtning från alla platser för log-Arkiv. Både primär och vänteläge databasen måste vara att hämta arkivera loggfiler från alla log Arkiv platser du vilken antingen av databasen instanser kan arkivera loggfiler. 

Arkivering av loggen utförs bara av den primära databasen. Om du ändrar HADR roller med database-servrar, eller om ett fel inträffar, ansvarar den nya primära databasen för arkivering av loggen. Om du har konfigurerat annan logg Arkiv platser loggarna kan arkiveras två gånger och när det gäller lokal eller fjärransluten Missade du kan behöva manuellt kopiera arkiverade loggar från den gamla primära servern till den aktiva platsen för den nya primära servern.

Vi rekommenderar att du konfigurerar vanliga NFS-resurs där loggarna skrivs från båda noderna. NFS måste ha hög tillgänglighet. 

Du kan använda befintliga med hög tillgänglighet NFS som används för transporter användarprofilens katalog. Läs:

- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][nfs-ha] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) om hur du använder [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) att skapa NFS-resurser


## <a name="test-the-cluster-setup"></a>Testa konfiguration

Det här avsnittet beskrivs hur du kan testa din Db2 HADR-konfiguration. **Varje test förutsätter att du är inloggad som användarrot** och IBM Db2 primära körs på **azibmdb01** virtuell dator.

Den första statusen för alla testfall beskrivs här: (crm_mon - r eller crm status)

- **status för CRM** är en ögonblicksbild av Pacemaker status under körning 
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

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktionen DBACOCKPIT--> konfiguration--> Översikt som:

![DBACockpit - före migrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testa övertagande av IBM Db2.


> [!IMPORTANT] 
> Innan du startar testet ska du kontrollera att Pacemaker har inte alla misslyckade åtgärder (crm-status), och det finns inga Platsbegränsningar (rester av migreringen test) och IBM Db2 HADR synkroniseringen fungerar. Kontrollera med användaren db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrera noden som kör den primära Db2-databasen genom att köra följande kommando:
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

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktionen DBACOCKPIT--> konfiguration--> Översikt som: ![DBACockpit - Postmigrering](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Resursmigrering med ”crm resource migrera” skapar Platsbegränsningar. Platsbegränsningar bör tas bort. Om Platsbegränsningar inte raderas resursen kan inte återställa eller så kan du uppleva oönskad övertag. 

Migrera tillbaka till **azibmdb01** och rensa Platsbegränsningar
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- CRM-resurs migrera < res_name > <host> – skapar Platsbegränsningar och kan orsaka problem med ett övertagande av
- CRM resource Rensa < res_name > - rensar Platsbegränsningar
- rensning av CRM-resurser < res_name > – tar bort alla fel för resursen


### <a name="test-the-fencing-agent"></a>Testa att hägna in agenten

I det här fallet kan vi testa uppstår hägna in vilket rekommenderas för användning med SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Klusternod **azibmdb01** ska startas om. IBM Db2 primära HADR rollen ska flyttas till **azibmdb02**. När **azibmdb01** är tillbaka online, Db2 instansen kommer att flytta i rollen för en sekundär databas-instans. 

Se till att starta den manuellt med för det ärende Pacemaker-tjänsten inte startar automatiskt på den primära omstartad tidigare:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testa övertagande av ett manuell

Du kan testa övertagande av ett manuellt genom att stoppa tjänsten Pacemaker på **azibmdb01** nod:
<pre><code>service pacemaker stop</code></pre>

status på **azibmdb02**
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

Efter redundansen kan du kan starta tjänsten igen på **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Avsluta Db2-processen på noden som kör den primära databasen HADR

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

Pacemaker kommer att starta om den primära Db2-databasinstansen på samma nod eller växla över till den nod som kör den sekundära databasen instanced och ett fel rapporteras.

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


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>Avsluta Db2-processen på noden som kör sekundär databas-instans

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

Db2-instans get startas om till den sekundära rollen som den hade tilldelats innan

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



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop kraft på noden som kör den primära databas HADR-instansen

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


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>Krascha VM med omstart på noden som kör den primära databas HADR-instansen

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



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära HADR-databasinstansen med ”stopp”

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

I sådana fall identifierar Pacemaker att noden som kör den primära databas-instansen inte svarar.

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

Nästa steg är att söka efter en **dela hjärna** situation. När den kvarvarande noden är till att den nod som körde den primära databasinstansen senast är nere kan ska en redundansväxling av resurser köras
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


När det gäller ”stoppa” noden, har den felaktiga noden startas om via Azure-hanteringsverktyg (Portal, PowerShell, AzureCLI,...). Noden misslyckade ska starta Db2-instans i den sekundära rollen när den är online igen.

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
Den här dokumentationen:

- [Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurera Pacemaker på SUSE Linux ange
- pri
- Se Server i Azure])https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

