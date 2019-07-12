---
title: Konfigurera IBM Db2 HADR på Azure virtual machines (VM) på RHEL | Microsoft Docs
description: Skapa hög tillgänglighet för IBM Db2 LUW på Azure virtual machines (VM) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812134"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Hög tillgänglighet för IBM Db2 LUW på Azure Virtual Machines på Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2694118] | Red Hat Enterprise Linux tillägg för hög tillgänglighet i Azure |
| [1999351] | Felsökning av förbättrad Azure övervakning för SAP |
| [2233094] | DB6: SAP-program på Azure som använder IBM Db2 för Linux, UNIX- och Windows - ytterligare information |
| [1612105] | DB6: Vanliga frågor och svar på Db2 med HADR |


| Dokumentation | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Har alla nödvändiga SAP Notes för Linux |
| [Azure virtuella datorer, planering och implementering av SAP på Linux][planning-guide] guide |
| [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide] (den här artikeln) |
| [Azure-datorer databasdistribution management system(DBMS) för SAP på Linux][dbms-guide] guide |
| [SAP-arbetsbelastningar på Azure checklista för planering och distribution][azr-sap-plancheck] |
| [Översikt över tillägg för hög tillgänglighet för Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Hög tillgänglighet tillägg Administration][rhel-ha-admin] |
| [Referens för tillägg för hög tillgänglighet][rhel-ha-ref] |
| [Stöd för principer för RHEL-kluster för hög tillgänglighet – Microsoft Azure-datorer som medlemmar i ett kluster][rhel-azr-supp]
| [Installera och konfigurera en Red Hat Enterprise Linux 7.4 (och senare) hög tillgänglighet-kluster på Microsoft Azure][rhel-azr-inst]
| [IBM Db2 Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10,5][db2-hadr-10.5] |
| [Supportpolicy för RHEL-kluster för hög tillgänglighet – hantering av IBM Db2 för Linux, Unix- och Windows i ett kluster][rhel-db2-supp]



## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet, IBM Db2 LUW med HADR är installerad på minst två Azure-datorer, som är distribuerade i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) eller över [Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

På följande bild visar en konfiguration för två databasserver virtuella Azure-datorer. Både databasservern virtuella Azure-datorer har sina egna lagringsutrymme som är anslutet och är igång och körs. I HADR har en databasinstans i någon av virtuella Azure-datorer rollen för den primära instansen. Alla klienter är anslutna till primära-instansen. Alla ändringar i databastransaktioner sparas lokalt i transaktionsloggen Db2. Eftersom transaktionen loggposter sparas lokalt överförs posterna via TCP/IP till databasinstansen på andra databasservern, standby-server eller standby-instansen. Väntelägesinstansen uppdaterar den lokala databasen genom att återställa framåt överförda transaktionen loggposter. På så sätt kan hålls reservservern synkroniserade med den primära servern.

HADR är bara en replikering funktionalitet. Det har inga felidentifiering och inga automatiska övertagande eller växla över resurser. Ett övertagande av eller överföring till standby-servern måste initieras manuellt av en databasadministratör. Du kan använda Linux Pacemaker klusterfunktionen för att uppnå en automatisk ta över och felsökning. Pacemaker övervakar två database server-instanser. När den primära instansen går sönder Pacemaker initierar en *automatisk* HADR övertagande av standby-servern. Pacemaker innebär också att den virtuella IP-adressen tilldelas till den nya primära servern.

![Översikt över hög tillgänglighet för IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Ha SAP-programservrar ansluta till den primära databasen, behöver du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av redundans ansluter SAP-programservrarna till den nya primära databasinstansen. I en Azure-miljö, en [Azure-belastningsutjämnare](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) krävs för att använda en virtuell IP-adress på sätt som krävs för HADR IBM Db2. 

Följande bild visar en översikt över en högtillgänglig installation av ett SAP-system baserat på IBM Db2-databas så att du förstår hur IBM Db2 LUW med HADR och Pacemaker passar in i en högtillgänglig installation av SAP-system. Den här artikeln beskriver bara IBM Db2, men den innehåller referenser till andra artiklar om hur du ställer in andra komponenter i ett SAP-system.

![IBM DB2 översikt över hela miljön i hög tillgänglighet](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Översikt över steg som krävs
Om du vill distribuera en IBM Db2-konfiguration, måste du följa dessa steg:

  + Planera din miljö.
  + Distribuera de virtuella datorerna.
  + Uppdatera RHEL Linux och konfigurera filsystem.
  + Installera och konfigurera Pacemaker.
  + Installationsprogrammet [glusterfs kluster][glusterfs] or [Azure NetApp Files][anf-rhel]
  + Installera [ASCS/ÄNDARE på ett separat kluster][ascs-ha-rhel].
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
| Virtuella värdnamn och virtuella IP-Adressen för IBM Db2-databas| Det virtuella IP- eller namnet som används för anslutning av SAP-programservrar. **DB-virt-värdnamnet**, **db-virt-IP-** . |
| Att hägna in Azure | Metod för att undvika delad hjärna situationer förhindras. |
| Azure Load Balancer | Användning av Basic eller Standard (rekommenderas), avsökningsport för Db2-databas (vår rekommendation 62500) **avsökningsporten**. |
| Namnmatchning| Hur namnmatchningen fungerar i miljön. DNS-tjänsten rekommenderas starkt. Lokala värdfil som kan användas. |
    
Mer information om Linux Pacemaker i Azure finns i [konfigurerar Pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Distribution på Red Hat Enterprise Linux

Resurs-agenten för IBM Db2 LUW ingår i Red Hat Enterprise Linux Server HA tillägg. För installation som beskrivs i det här dokumentet, bör du använda Red Hat Enterprise Linux för SAP. Azure Marketplace innehåller en bild för Red Hat Enterprise Linux 7.4 för SAP eller senare som du kan använda för att distribuera nya virtuella Azure-datorer. Tänk på de olika modellerna support eller tjänst som erbjuds av Red Hat via Azure Marketplace när du väljer en VM-avbildning i Azure Marketplace för virtuella datorer.

### <a name="hosts-dns-updates"></a>Värdar: DNS-uppdateringar
Se en lista över alla värdnamn, inklusive virtuella värdnamn och uppdatera DNS-servrarna för att aktivera rätt IP-adress till värd-namnmatchning. Om en DNS-server inte finns eller du kan inte uppdatera och skapa DNS-poster kan behöva du använda lokal värd-filer för de enskilda virtuella datorer som deltar i det här scenariot. Om du använder värdposter för filer, se till att posterna tillämpas på alla virtuella datorer i miljön för SAP-system. Vi rekommenderar dock att du använder din DNS-server som helst utökar till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrollera att den valda OS stöds av IBM/SAP för IBM Db2 LUW. Lista över operativsystemversioner som stöds för virtuella Azure-datorer och Db2 versioner finns i SAP-kommentar [1928533]. Listan över versioner av enskilda Db2 version är tillgänglig i matrisen SAP produkten tillgänglighet. Vi rekommenderar minst Red Hat Enterprise Linux 7.4 för SAP på grund av Azure-relaterade prestandaförbättringar i det här eller senare versioner av Red Hat Enterprise Linux.

1. Skapa eller välj en resursgrupp.
1. Skapa eller välj ett virtuellt nätverk och undernät.
1. Skapa en Azure-tillgänglighetsuppsättning eller distribuera en tillgänglighetszon.
    + Ange de maximala uppdateringsdomäner för tillgänglighetsuppsättning, till 2.
1. Skapa virtuell dator 1.
    + Använd Red Hat Enterprise Linux för SAP-avbildning i Azure Marketplace.
    + Välj den Azure-tillgänglighetsuppsättning som du skapade i steg 3, eller Tillgänglighetszon.
1.  Skapa virtuell dator 2.
    + Använd Red Hat Enterprise Linux för SAP-avbildning i Azure Marketplace.
    + Välj den Azure-tillgänglighetsuppsättning du i skapade i steg 3 eller välja Tillgänglighetszon (inte i samma zon som i steg 3).
1. Lägga till datadiskar till de virtuella datorerna och sedan kontrollera rekommendation av en fil systeminställningarna i artikeln [IBM Db2 Azure virtuella datorer DBMS-distribution för SAP-arbetsbelastningar][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Skapa Pacemaker-kluster
    
För att skapa en grundläggande Pacemaker kluster för den här IBM Db2-servern, se [konfigurerar Pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Red Hat-brandväggsregler
Red Hat Enterprise Linux har brandvägg är aktiverad som standard. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationen tips för att konfigurera IBM Db2 LUW med HADR

Du ställer in den primära IBM Db2 LUW-databasinstansen:

- Använd alternativet hög tillgänglighet eller distribuerade.
- Installera SAP ASCS/ÄNDARE och Database-instansen.
- Ta en säkerhetskopia av den nyligen installerade databasen.

> [!IMPORTANT] 
> Anteckna ”databas kommunikationsporten” som anges under installationen. Det måste vara samma portnummer för båda databasinstanser.
>![SAP SWPM Port Definition](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR inställningar för Azure

   När du använder en Azure-Pacemaker hägna in agent kan du ange följande parametrar:

   - HADR peer fönstret varaktighet (sekunder) (HADR_PEER_WINDOW) = 240  
   - HADR timeout-värdet (HADR_TIMEOUT) = 45

Vi rekommenderar att parametrarna föregående baserat på första redundans/övertagande av testet. Det är obligatoriskt att du testar för redundans och ta över fungera korrekt med de här parameterinställningarna. Eftersom enskilda konfigurationer kan variera, kräva parametrarna för justering. 

> [!NOTE]
> Specifika till IBM Db2 med HADR konfiguration med normal start: Den sekundära eller standby-database-instansen måste vara igång innan du kan starta den primära databas-instansen.

   
> [!NOTE]
> För installation och konfiguration som är specifik för Azure och Pacemaker: Det finns en explicit fråga om hög tillgänglighet för IBM Db2 LUW under installationsproceduren via SAP Software etablering Manager:
>+ Markera inte **IBM Db2 pureScale**.
>+ Markera inte **installera IBM Tivoli automatisering i System för Multiplatforms**.
>+ Markera inte **generera konfigurationsfiler för kluster**.
>![SAP SWPM - DB2 HA alternativ](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Om du vill konfigurera databasservern vänteläge med hjälp av SAP homogena system kopia proceduren, kör du dessa steg:

1. Välj den **System copy** alternativet > **rikta system** > **distribuerade** > **databasinstansen**.
1. Som en kopieringsmetod för att, Välj **homogena System** så att du kan använda Säkerhetskopiering för att återställa en säkerhetskopia på reservserverinstansen.
1. När du når det avsluta steget för att återställa databasen för homogena system kopia avsluta installationsprogrammet. Återställ databasen från en säkerhetskopia av den primära värddatorn. Alla efterföljande installation faser har redan körts på den primära databas-servern.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat brandväggsregler för DB2 HADR
Lägga till brandväggsregler för att tillåta trafik till DB2 och mellan DB2 för HADR ska fungera:
+ Kommunikationsport för databasen. Om du använder partitioner, lägger du till dessa portar för.
+ HADR port (värdet för DB2 parametern HADR_LOCAL_SVC)
+ Azure avsökningsport
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR kontroll
För exempelsyfte och de förfaranden som beskrivs i den här artikeln kan databasen SID är **ID2**.

När du har konfigurerat HADR och statusen är PEER- och ansluten på primär och vänteläge noderna, kontrollera följande:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker konfiguration

När du använder Pacemaker för automatisk redundans i händelse av ett nodfel, måste du konfigurera din Db2-instanser och Pacemaker enligt detta. Det här avsnittet beskriver den här typen av konfiguration.

Följande objekt har ett prefix med antingen:

- **[A]** : Gäller för alla noder
- **[1]** : Gäller endast för nod 1 
- **[2]** : Gäller endast för nod 2

**[A]**  Krav för Pacemaker konfiguration:
1. Stäng av båda databasservrar med användaren db2\<sid > med db2stop.
1. Ändra gränssnittsmiljön för db2\<sid > användaren */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker konfiguration

**[1]**  IBM Db2 HADR-specifika Pacemaker konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]**  Skapa IBM Db2-resurser:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]**  Starta IBM Db2-resurser:
* Placera Pacemaker från underhållsläget.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]**  Kontrollera att klusterstatusen är OK och att alla resurser har startats. Det är inte viktigt vilken nod som resurserna som körs på.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 az-idb02]

Fullständig lista över resurser:

 rsc_st_azure   (stonith:fence_azure_arm):      Igång az-idb01 Master/Slave-uppsättning: Huvudservrar Db2_HADR_ID2-master [Db2_HADR_ID2]: [az-idb01] Slaves: [az-idb02] resursgrupp: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2):       Igång az idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb):      Igång az-idb01

Daemon-Status: corosync: aktiv/inaktiverad pacemaker: aktiv/inaktiverad pcsd: aktiv/aktiverat
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

**[A]**  Lägg till brandväggsregel för avsökningsporten:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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

Vi rekommenderar att du konfigurerar en NFS-resurs eller en GlusterFS, där loggarna skrivs från båda noderna. NFS-resursens eller GlusterFS måste ha hög tillgänglighet. 

Du kan använda befintliga med hög tillgänglighet NFS-resurser eller GlusterFS för transporter eller en profilkatalog. Mer information finns i:

- [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver][glusterfs] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program][anf-rhel]
- [Azure NetApp filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (för att skapa NFS-resurser)

## <a name="test-the-cluster-setup"></a>Testa konfiguration

Det här avsnittet beskrivs hur du kan testa din Db2 HADR-konfiguration. Varje test förutsätter IBM Db2 primära körs på den *az idb01* virtuell dator. Användare med sudo-behörighet eller rot (rekommenderas inte) måste användas.

Den första statusen för alla testfall beskrivs här: (crm_mon - r eller datorer status)

- **status för datorer** är en ögonblicksbild av status för Pacemaker vid tiden för körningen 
- **crm_mon - r** är kontinuerlig utdata från Pacemaker status

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktionen DBACOCKPIT > konfiguration > Översikt, enligt följande bild:

![DBACockpit - före migrering](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Testa övertagande av IBM Db2


> [!IMPORTANT] 
> Kontrollera följande innan du startar testet:
> * Pacemaker har inte alla misslyckade åtgärder (datorer status).
> * Det finns inga Platsbegränsningar (rester av migreringen test)
> * IBM Db2 HADR synkroniseringen fungerar. Kontrollera med användaren db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrera den nod som kör den primära Db2-databasen genom att köra följande kommando:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

När migreringen är klar, crm-status-utdata som ser ut som:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Den ursprungliga statusen i ett SAP-system dokumenteras i transaktionen DBACOCKPIT > konfiguration > Översikt, enligt följande bild:

![DBACockpit - Postmigrering](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Resursmigrering med ”datorer för att flytta resursen” skapar Platsbegränsningar. Platsbegränsningar förhindrar i det här fallet IBM Db2-instans som körs på az idb01. Om Platsbegränsningar inte raderas kan resursen inte återställa.

Ta bort platsen begränsa och vänteläge noden kommer att startas på az idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
Och klustret status ändras till:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - borttagna platsen begränsa](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrera resursen till *az idb01* och avmarkera Platsbegränsningar
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **för att flytta datorer resursen \<res_name > <host>:** Skapar Platsbegränsningar och kan orsaka problem med ett övertagande av
- **datorer resource Rensa \<res_name >** : Tar bort Platsbegränsningar
- **rensning av resurser datorer \<res_name >** : Tar bort alla fel för resursen

### <a name="test-a-manual-takeover"></a>Testa övertagande av ett manuell

Du kan testa övertagande av ett manuellt genom att stoppa tjänsten Pacemaker på *az idb01* nod:
<pre><code>systemctl stop pacemaker</code></pre>

status på *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Efter redundansen kan du kan starta tjänsten igen på *az idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Avsluta Db2-processen på den nod som kör den primära databasen HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2-instansen kommer att misslyckas och Pacemaker flyttas huvudnoden och rapportera följande status:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker startar om den primära Db2-databasinstansen på samma nod eller den växlar över till den nod som kör den sekundära databas-instansen och ett fel rapporteras.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Avsluta Db2-processen på den nod som kör sekundär databas-instans

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Noden hämtar into misslyckades angivna och fel rapporteras
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2-instans hämtar startas om i den sekundära rollen som den hade tilldelats innan.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop kraft på den nod som kör HADR primära databasinstans

Som användare db2\<sid > köra kommandot db2stop kraft:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Enhetsfel har identifierats:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Den sekundära databasinstansen för Db2 HADR har befordras till den primära rollen.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära HADR-databasinstansen med ”stopp”

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

I sådana fall identifierar Pacemaker att den nod som kör den primära databas-instansen inte svarar.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Nästa steg är att söka efter en *dela hjärna* situation. När den kvarvarande noden har fastställt att den nod som sista körde den primära databas-instansen inte är tillgänglig, körs en redundansväxling av resurser.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


I händelse av ett kernel panic-meddelande, kommer du restared noden misslyckade av hägna in agenten. När den felaktiga noden är online igen, måste du starta pacemaker kluster efter
<pre><code>sudo pcs cluster start</code></pre> Db2-instans i den sekundära rollen startar.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Nästa steg
- [Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr]
