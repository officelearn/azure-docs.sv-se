---
title: Konfigurera IBM Db2 HADR på virtuella Azure-datorer (VMs) på RHEL | Microsoft-dokument
description: Upprätta hög tillgänglighet för IBM Db2 LUW på Azure virtuella datorer (VMs) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598705"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7.x: Installation och uppgradering |
| [2694118] | Red Hat Enterprise Linux HA-tillägg på Azure |
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
| [Översikt över tillägget Hög tillgänglighet för Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Administration av tillägg med hög tillgänglighet][rhel-ha-admin] |
| [Tilläggsreferens för hög tillgänglighet][rhel-ha-ref] |
| [Supportprinciper för RHEL-kluster med hög tillgänglighet – Virtuella Microsoft Azure-datorer som klustermedlemmar][rhel-azr-supp]
| [Installera och konfigurera ett Red Hat Enterprise Linux 7.4 -kluster med hög tillgänglighet på Microsoft Azure][rhel-azr-inst]
| [DBMS-distribution för SAP-arbetsbelastning för IBM Db2 på virtuella Azure-datorer][dbms-db2] |
| [IBM Db2 HADR 11,1][db2-hadr-11.1] |
| [IBM Db2 HADR 10,5][db2-hadr-10.5] |
| [Supportprincip för RHEL-kluster med hög tillgänglighet – hantering av IBM Db2 för Linux, Unix och Windows i ett kluster][rhel-db2-supp]



## <a name="overview"></a>Översikt
För att uppnå hög tillgänglighet installeras IBM Db2 LUW med HADR på minst två virtuella Azure-datorer som distribueras i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) eller över [Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Följande grafik visar en installation av två virtuella Azure-datorer för databasservern. Båda databasservern Azure virtuella datorer har sin egen lagring ansluten och är igång. I HADR har en databasinstans i en av Azure virtuella datorer rollen som primär instans. Alla klienter är anslutna till primär instans. Alla ändringar i databastransaktioner sparas lokalt i db2-transaktionsloggen. När transaktionsloggposterna sparas lokalt överförs posterna via TCP/IP till databasinstansen på den andra databasservern, vänteservern eller standby-instansen. Väntelägesinstansen uppdaterar den lokala databasen genom att överföra de överförda transaktionsloggposterna framåt. På så sätt synkroniseras standby-servern med den primära servern.

HADR är bara en replikeringsfunktion. Den har ingen feldetektering och inga automatiska uppköps- eller redundansanläggningar. Ett övertagande eller en överföring till vänteservern måste initieras manuellt av en databasadministratör. För att uppnå en automatisk uppköps- och felidentifiering kan du använda Klustringsfunktionen för Linux Pacemaker. Pacemaker övervakar de två databasserverinstanserna. När den primära databasserverinstansen kraschar initierar Pacemaker ett *automatiskt* HADR-övertagande av standby-servern. Pacemaker ser också till att den virtuella IP-adressen tilldelas den nya primära servern.

![Översikt över IBM Db2 med hög tillgänglighet](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Om du vill att SAP-programservrar ska ansluta till primär databas behöver du ett virtuellt värdnamn och en virtuell IP-adress. I händelse av en redundans ansluter SAP-programservrarna till en ny primär databasinstans. I en Azure-miljö krävs en [Azure-belastningsutjämnare](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) för att använda en virtuell IP-adress på det sätt som krävs för HADR för IBM Db2. 

För att hjälpa dig att till fullo förstå hur IBM Db2 LUW med HADR och Pacemaker passar in i en högtillgänglig SAP-systeminstallation, presenterar följande avbildning en översikt över en mycket tillgänglig installation av ett SAP-system baserat på IBM Db2-databas. Den här artikeln täcker endast IBM Db2, men den innehåller referenser till andra artiklar om hur du ställer in andra komponenter i ett SAP-system.

![IBM DB2 hög tillgänglighet full miljö översikt](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Översikt på hög nivå över de steg som krävs
Om du vill distribuera en IBM Db2-konfiguration måste du följa dessa steg:

  + Planera din miljö.
  + Distribuera de virtuella datorerna.
  + Uppdatera RHEL Linux och konfigurera filsystem.
  + Installera och konfigurera Pacemaker.
  + Konfigurera [glusterfs-kluster][glusterfs] eller [Azure NetApp-filer][anf-rhel]
  + Installera [ASCS/ERS i ett separat kluster][ascs-ha-rhel].
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
| Azure stängsel | Metod för att undvika delade hjärnsituationer förhindras. |
| Azure Load Balancer | Användning av Basic eller Standard (rekommenderas), sondport för Db2-databas (vår rekommendation 62500) **probe-port**. |
| Namnmatchning| Hur namnmatchning fungerar i miljön. DNS-tjänsten rekommenderas starkt. Lokal hosts-fil kan användas. |
    
Mer information om Linux Pacemaker i Azure finns i [Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Distribution på Red Hat Enterprise Linux

Resursagenten för IBM Db2 LUW ingår i Red Hat Enterprise Linux Server HA Addon. För den konfiguration som beskrivs i det här dokumentet bör du använda Red Hat Enterprise Linux för SAP. Azure Marketplace innehåller en avbildning för Red Hat Enterprise Linux 7.4 för SAP eller högre som du kan använda för att distribuera nya virtuella Azure-datorer. Var medveten om de olika support- eller tjänstmodeller som erbjuds av Red Hat via Azure Marketplace när du väljer en VM-avbildning på Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Värdar: DNS-uppdateringar
Gör en lista över alla värdnamn, inklusive virtuella värdnamn, och uppdatera DNS-servrarna så att korrekt IP-adress kan vara värdnamnsmatchning. Om det inte finns en DNS-server eller om du inte kan uppdatera och skapa DNS-poster måste du använda de lokala värdfilerna för de enskilda virtuella datorer som deltar i det här scenariot. Om du använder värdfilposter kontrollerar du att posterna tillämpas på alla virtuella datorer i SAP-systemmiljön. Vi rekommenderar dock att du använder din DNS som helst sträcker sig till Azure


### <a name="manual-deployment"></a>Manuell distribution

Kontrollera att det valda operativsystemet stöds av IBM/SAP för IBM Db2 LUW. Listan över operativsystemversioner som stöds för Virtuella Azure-datorer och Db2-versioner finns i SAP note [1928533]. Listan över OS-utgåvor av enskilda Db2-versioner finns i SAP-produkttillgänglighetsmatrisen. Vi rekommenderar starkt ett minimum av Red Hat Enterprise Linux 7.4 för SAP på grund av Azure-relaterade prestandaförbättringar i den här eller senare Red Hat Enterprise Linux-versionerna.

1. Skapa eller välj en resursgrupp.
1. Skapa eller välj ett virtuellt nätverk och undernät.
1. Skapa en Azure-tillgänglighetsuppsättning eller distribuera en tillgänglighetszon.
    + För tillgänglighetsuppsättningen anger du de maximala uppdateringsdomänerna till 2.
1. Skapa virtuell dator 1.
    + Använd Red Hat Enterprise Linux för SAP-avbildning på Azure Marketplace.
    + Välj den Azure-tillgänglighetsuppsättning som du skapade i steg 3 eller välj Tillgänglighetszon.
1.  Skapa virtuell dator 2.
    + Använd Red Hat Enterprise Linux för SAP-avbildning på Azure Marketplace.
    + Välj azure-tillgänglighetsuppsättningen som du har skapat i steg 3 eller välj Tillgänglighetszon (inte samma zon som i steg 3).
1. Lägg till datadiskar till de virtuella datorerna och kontrollera sedan rekommendationen för en filsystemkonfiguration i artikeln [IBM Db2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Skapa pacemakerklustret
    
Information om hur du skapar ett grundläggande Pacemaker-kluster för den här IBM Db2-servern finns i [Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Red Hat brandväggsregler
Red Hat Enterprise Linux har brandvägg aktiverad som standard. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installationstips för att ställa in IBM Db2 LUW med HADR

Så här konfigurerar du den primära IBM Db2 LUW-databasinstansen:

- Använd alternativet hög tillgänglighet eller distribuerad.
- Installera SAP ASCS/ERS och Database-instansen.
- Gör en säkerhetskopia av den nyinstallerade databasen.

> [!IMPORTANT] 
> Skriv ner "Databaskommunikationsporten" som är inställd under installationen. Det måste vara samma portnummer för båda databasinstanserna.
>![DEFINITION AV SAP SWPM-port](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR-inställningar för Azure

   När du använder en Azure Pacemaker-stängselagent ställer du in följande parametrar:

   - HADR-peer-fönstrets varaktighet (sekunder) (HADR_PEER_WINDOW) = 240  
   - TIMEOUT-värde för HADR (HADR_TIMEOUT) = 45

Vi rekommenderar föregående parametrar baserat på inledande redundans-/uppköpstestning. Det är obligatoriskt att du testar för korrekt funktionalitet vid redundans och uppköp med dessa parameterinställningar. Eftersom enskilda konfigurationer kan variera kan parametrarna kräva justering. 

> [!NOTE]
> Specifik för IBM Db2 med HADR-konfiguration med normal start: Den sekundära eller standby-databasinstansen måste vara igång innan du kan starta den primära databasinstansen.

   
> [!NOTE]
> För installation och konfiguration som är specifik för Azure och Pacemaker: Under installationsproceduren via SAP Software Provisioning Manager finns det en tydlig fråga om hög tillgänglighet för IBM Db2 LUW:
>+ Välj inte **IBM Db2 pureScale**.
>+ Välj inte **Installera IBM Tivoli System Automation för multiplattformar**.
>+ Välj inte **Generera klusterkonfigurationsfiler**.
>![SAP SWPM - DB2 HA alternativ](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Så här konfigurerar du databasen för vänteläge med hjälp av sap-homogena systemkopieringsproceduren:

1. Välj alternativet **Systemkopiering** > **inriktningssystem** > **distribuerad** > **databasinstans**.
1. Som kopieringsmetod väljer du **Homogent system** så att du kan använda säkerhetskopian för att återställa en säkerhetskopia på serverinstansen för vänteläge.
1. När du når avslutningssteget för att återställa databasen för homogen systemkopia avslutar du installationsprogrammet. Återställ databasen från en säkerhetskopia av den primära värden. Alla efterföljande installationsfaser har redan körts på den primära databasservern.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat brandvägg regler för DB2 HADR
Lägg till brandväggsregler så att trafik till DB2 och mellan DB2 för HADR fungerar:
+ Databaskommunikationsport. Om du använder partitioner lägger du till dessa portar också.
+ HADR-port (värdet för DB2-parametern HADR_LOCAL_SVC)
+ Azure-avsökningsport
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR kontroll
För demonstrationsändamål och de förfaranden som beskrivs i den här artikeln är databasen SID **ID2**.

När du har konfigurerat HADR och statusen är PEER och ANSLUTEN på de primära noderna och standby-noderna utför du följande kontroll:

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



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker-konfiguration

När du använder Pacemaker för automatisk redundans i händelse av ett nodfel måste du konfigurera db2-instanserna och pacemakern därefter. I det här avsnittet beskrivs den här typen av konfiguration.

Följande objekt föregås av antingen:

- **[A]**: Gäller för alla noder
- **[1]**: Gäller endast för nod 1 
- **[2]**: Gäller endast för nod 2

**[A]** Förutsättning för Pacemaker-konfiguration:
1. Stäng av båda databasservrarna\<med användare db2 sid> med db2stop.
1. Ändra skalmiljön för\<db2 sid> användare till */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker-konfiguration

**[1]** IBM Db2 HADR-specifik Pacemaker-konfiguration:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Skapa IBM Db2-resurser:
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

**[1]** Starta IBM Db2-resurser:
* Lägg pacemakern ur underhållsläge.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Kontrollera att klusterstatusen är OK och att alla resurser har startats. Det är inte viktigt vilken nod resurserna körs på.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Fullständig lista över resurser:

 rsc_st_azure (stonith:fence_azure_arm): Startade az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slavar: [ az-idb02 ] Resursgrupp: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Startade az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Startade az-idb01

Daemon Status: corosync: aktiv/inaktiverad pacemaker: aktiv/inaktiverad dator: aktiv/aktiverad
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

**[A]** Lägg till brandväggsregel för avsökningsport:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Logga in på den primära programservern för J2EE-instansen och kör:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. Välj **säkerhetsarkivet**i den vänstra ramen .
1. Välj nyckeln `jdbc/pool/\<SAPSID>/url`i den högra ramen .
1. Ändra värdnamnet i JDBC-URL:en till det virtuella värdnamnet.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Välj **Lägg till**.
1. Om du vill spara ändringarna väljer du diskikonen längst upp till vänster.
1. Stäng konfigurationsverktyget.
1. Starta om Java-instansen.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurera loggarkivering för HADR-installation
Om du vill konfigurera Db2-loggarkiveringen för HADR-installationen rekommenderar vi att du konfigurerar både den primära och den standby-databasen så att den har automatisk logghämtningsfunktion från alla loggarkivplatser. Både den primära databasen och standby-databasen måste kunna hämta loggarkivfiler från alla loggarkivplatser som någon av databasinstanserna kan arkivera loggfiler till. 

Loggarkiveringen utförs endast av den primära databasen. Om du ändrar DATABASSERVRARNAS HADR-roller eller om ett fel inträffar, ansvarar den nya primära databasen för loggarkivering. Om du har konfigurerat flera loggarkivplatser kan dina loggar arkiveras två gånger. I händelse av en lokal eller fjärrinfångst kan du också behöva kopiera de arkiverade loggarna manuellt från den gamla primära servern till den aktiva loggplatsen för den nya primära servern.

Vi rekommenderar att du konfigurerar en gemensam NFS-resurs eller GlusterFS, där loggar skrivs från båda noderna. NFS-aktien eller GlusterFS måste vara mycket tillgängliga. 

Du kan använda befintliga NFS-resurser med högtillgänglig tillgång eller GlusterFS för transporter eller en profilkatalog. Mer information finns i:

- [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver][glusterfs] 
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program][anf-rhel]
- [Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (för att skapa NFS-resurser)

## <a name="test-the-cluster-setup"></a>Testa klusterinställningarna

I det här avsnittet beskrivs hur du kan testa din Db2 HADR-konfiguration. Varje test förutsätter IBM Db2 primära körs på *az-idb01* virtuell dator. Användare med sudo-behörighet eller -rot (rekommenderas inte) måste användas.

Den ursprungliga statusen för alla testfall förklaras här: (crm_mon -r- eller pc-status)

- **pcs status** är en ögonblicksbild av Pacemaker status vid körning 
- **crm_mon -r** är kontinuerlig utgång av Pacemaker-status

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

Den ursprungliga statusen i ett SAP-system dokumenteras i Transaction DBACOCKPIT > Configuration > Overview, som visas i följande bild:

![DBACockpit - Före migrering](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Test övertagande av IBM Db2


> [!IMPORTANT] 
> Innan du startar testet ska du se till att:
> * Pacemaker har inga misslyckade åtgärder (pcs status).
> * Det finns inga platsbegränsningar (rester av migreringstest)
> * IBM Db2 HADR-synkroniseringen fungerar. Kontrollera med användaren\<db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrera noden som kör den primära Db2-databasen genom att köra följande kommando:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

När migreringen är klar ser crm-statusutdata ut så här:
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

Den ursprungliga statusen i ett SAP-system dokumenteras i Transaction DBACOCKPIT > Configuration > Overview, som visas i följande bild:

![DBACockpit - Post Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Resursmigrering med "pcs resource move" skapar platsbegränsningar. Platsbegränsningar i det här fallet förhindrar att IBM Db2-instans körs på az-idb01. Om platsbegränsningar inte tas bort kan resursen inte återställas.

Ta bort platsbegränsningen och standby-noden startas på az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
Och klusterstatus ändras till:
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

![DBACockpit - Borttaget platsbegränsning](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrera resursen tillbaka till *az-idb01* och rensa platsbegränsningarna
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs resurs \<flytta <host>res_name>:** Skapar platsbegränsningar och kan orsaka problem med uppköp
- **pcs-resursen \<rensar res_name>**: Rensar platsbegränsningar
- **st resursrensning \<res_name>: **Rensar alla fel i resursen

### <a name="test-a-manual-takeover"></a>Testa ett manuellt övertagande

Du kan testa ett manuellt uppköp genom att stoppa *Pacemaker-tjänsten på az-idb01-noden:*
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

Efter redundansen kan du starta tjänsten igen på *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Döda Db2-processen på noden som kör HADR-primärdatabasen

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2-instansen kommer att misslyckas och Pacemaker flyttar huvudnoden och rapporterar följande status:

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

Pacemaker startar om db2-primärdatabasinstansen på samma nod, eller så växlar den över till den nod som kör den sekundära databasinstansen och ett fel rapporteras.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Döda Db2-processen på noden som kör den sekundära databasinstansen

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Noden hamnar i felaktigt angivet och felrapporterat
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

Db2-instansen startas om i den sekundära rollen som den hade tilldelat tidigare.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stoppa DB via db2stop-kraft på noden som kör den primära HADR-databasinstansen

Som användare\<db2 sid> utföra kommandot db2stop kraft:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Fel upptäcktes:

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

Db2 HADR-sekundär databasinstansen befordrades till den primära rollen.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Krascha den virtuella datorn som kör den primära HADR-databasinstansen med "stopp"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

I så fall upptäcker Pacemaker att noden som kör den primära databasinstansen inte svarar.

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

Nästa steg är att leta efter en *delad hjärnsituation.* När den efterlevande noden har fastställt att noden som senast körde den primära databasinstansen är nere, körs en redundans av resurser.

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


I händelse av kernel-panik kommer den misslyckade noden att vilas av fäktningsagenten. När den misslyckade noden är online igen måste du starta pacemakerklustret
<pre><code>sudo pcs cluster start</code></pre> Den startar Db2-instansen i den sekundära rollen.

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
- [Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure][rhel-pcs-azr]
