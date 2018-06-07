---
title: Hög tillgänglighet som konfigurerats med STONITH för SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Skapa hög tillgänglighet för SAP HANA i Azure (stora instanser) i SUSE med hjälp av STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 344a48ff82bd93bf8dc9924e09399e72b9f88e2f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656371"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Hög tillgänglighet som angetts i SUSE med hjälp av STONITH
Det här dokumentet innehåller detaljerade steg-för-steg-instruktioner att ställa in en hög tillgänglighet på SUSE operativsystemet med STONITH enheten.

**FRISKRIVNING:** *handboken härleds genom att testa inställningarna i Microsoft HANA stora instanser-miljön, som fungerar korrekt. Eftersom Microsoft Service Management-teamet för HANA stora instanser inte har stöd för operativsystem, kan du behöver kontakta SUSE för felsökningsinformation eller information på operativsystemet lager. Microsoft service management-teamet konfigurera STONITH enhet och fullständigt stöder och kan vara inblandade för felsökning av problem med STONITH enheter.*
## <a name="overview"></a>Översikt
Om du vill konfigurera hög tillgänglighet via SUSE kluster uppfylla följande krav.
### <a name="pre-requisites"></a>Förutsättningar
- HANA stora instanser har etablerats
- Operativsystemet har registrerats
- HANA stora instanser servrarna är anslutna till SMT-servern för att hämta korrigeringsfiler-paket
- Operativsystemet har senaste korrigeringsprogram som är installerade
- NTP (tidsserver) har konfigurerats
- Läs och förstå den senaste versionen av SUSE dokumentationen på konfiguration för hög tillgänglighet

### <a name="setup-details"></a>Konfigurera information
Den här guiden använder följande inställningar:
- Operativsystem: SLES 12 SP1 för SAP
- HANA stora instanser: 2xS192 (fyra sockets, 2 TB)
- HANA Version: HANA 2.0 SP1
- Servernamn: sapprdhdb95 (Nod1) och sapprdhdb96 (node2)
- STONITH enhet: iSCSI-baserade STONITH enhet
- Ställ in på en av noderna HANA stora instans NTP

När du ställer in HANA stora instanser med HSR kan du begära Microsoft Service Management-teamet för att ställa in STONITH. Om du redan är en befintlig kund som har HANA stora instanser etablerad och behov STONITH enhet har ställts in för din befintliga blad, måste du ange följande information till Microsoft Service Management-teamet i formuläret service (SRF). Du kan begära SRF formulär via Technical-kontoansvarige eller din Microsoft-Contact för stora HANA-instans. Nya kunder kan begära STONITH enheten vid tidpunkten för etablering. Indata är tillgängliga i formuläret etablering.

- Servernamnet och Server-IP-adress (till exempel myhanaserver1, 10.35.0.1)
- Plats (till exempel oss East)
- Kundens namn (till exempel Microsoft)
- SID - HANA System-ID (till exempel H11)

När du har konfigurerat STONITH enheten tillhandahåller Microsoft Service Management-teamet du uppstår enhetens namn och IP-adress för iSCSI-lagring som du kan använda för att konfigurera STONITH installation. 

Följande steg måste följas för att ställa in den slutpunkt till slutpunkt hög tillgänglighet med hjälp av STONITH:

1.  Identifiera uppstår-enhet
2.  Initiera uppstår enheten
3.  Konfigurera klustret
4.  Ställa in Softdog Watchdog
5.  Ansluta till noden i klustret
6.  Validera att klustret
7.  Konfigurera resurserna i klustret
8.  Testa redundans-processen

## <a name="1---identify-the-sbd-device"></a>1.   Identifiera uppstår-enhet
Det här avsnittet beskrivs om att avgöra uppstår enheten för din konfiguration när Microsoft service management-teamet har konfigurerats på STONITH. **Det här avsnittet gäller endast för den befintliga kunden**. Om du är en ny kund Microsoft service management-teamet tillhandahåller uppstår enhetsnamn för dig och du kan hoppa över det här avsnittet.

1.1 ändra */etc/iscsi/initiatorname.isci* till 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft service management ger den här strängen. Ändra den här filen på **både** noder, men värdet för noden är något annat på varje nod.

![initiatorname.PNG](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 ändra */etc/iscsi/iscsid.conf*: Ange *node.session.timeo.replacement_timeout=5* och *node.startup = automatisk*. Ändra den här filen på **både** noder.

1.3 köra kommandot identifiering, den visar fyra sessioner. Kör det på båda noderna.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Kör kommando för att logga in på iSCSI-enhet, den visar fyra sessioner. Kör det på **både** noder.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 kör skriptet genomsökningen: *genomsökningen-scsi-bus.sh*.  Det här skriptet visar de nya diskar som skapats åt dig.  Kör det på båda noderna. Du bör se ett LUN-nummer som är större än noll (till exempel: 1, 2 osv.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.PNG](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 används för att hämta namnet på en enhet kör kommandot *fdisk – l*. Kör det på båda noderna. Välj enheten med storleken på **178 MiB**.

```
  fdisk –l
```

![Fdisk l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Initiera uppstår enheten

2.1 initiera uppstår enheten på **både** noder

```
sbd -d <SBD Device Name> create
```
![sbdcreate.PNG](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Kontrollera vad som har skrivits till enheten. Gör på **både** noder

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurera klustret
Det här avsnittet beskriver stegen för att ställa in SUSE HA klustret.
### <a name="31-package-installation"></a>3.1 installationen
3.1.1 Kontrollera att ha_sles och SAPHanaSR doc mönster är installerade. Installera dem om det inte är installerat. Installera den på **både** noder.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.PNG](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 skapa klustret
3.2.1 du kan använda *ha kluster-initiering* kommando eller använda guiden yast2 för att skapa klustret. I det här fallet används guiden yast2. Du utför det här steget **endast på den primära noden**.

Följ yast2 > hög tillgänglighet > klustret ![yast-kontroll-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klicka på **Avbryt** eftersom halk2-paketet har redan installerats.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klicka på **fortsätta**

Förväntat värde = antalet noder som distribuerats (i det här fallet 2) ![yast-kluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) klickar du på **nästa**
![yast-kluster-konfigurera-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Lägg till nod-namn och klicka sedan på ”Lägg till föreslagna filer”

Klicka på ”Aktivera csync2”

Klicka på ”Skapa Förproduktion Shared nycklar” visas under popup-fönster

![yast-nyckel-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klicka på **OK**

Autentiseringen utförs med IP-adresser och Förproduktion shared nycklar i Csync2. Nyckelfilen genereras med csync2 -k /etc/csync2/key_hagroup. Filen key_hagroup ska kopieras till alla medlemmar i klustret manuellt när den har skapats. **Se till att kopiera filen från nod 1 till nod 2**.

![yast-kluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klicka på **nästa**
![yast-kluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Startar var inaktiverat i standardalternativet, ändra den till ”on” så pacemaker har startats på Start. Du kan göra valet baserat på dina krav för installationen.
Klicka på **nästa** och klusterkonfigurationen har slutförts.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Ställa in Softdog Watchdog
Det här avsnittet beskrivs konfigurationen av watchdog (softdog).

4.1 Lägg till följande rad */etc/init.d/boot.local* på **både** noder.
```
modprobe softdog
```
![modprobe softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 uppdatera filen */etc/sysconfig/sbd* på **både** noder som följande:
```
SBD_DEVICE="<SBD Device Name>"
```
![uppstår device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 läsa in modulen kernel på **både** noder genom att köra följande kommando
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 och kontrollera att softdog körs som följande på **både** noder:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 starta enheten uppstår på **både** noder
```
/usr/share/sbd/sbd.sh start
```
![uppstår-v-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 testa daemonen uppstår på **både** noder. Du ser två poster när du har konfigurerat på **både** noder
```
sbd -d <SBD Device Name> list
```
![uppstår list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 skicka ett testmeddelande till **en** noder
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![uppstår list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 på den **andra** noder (nod 2) du kan kontrollera status för meddelande
```
sbd  -d <SBD Device Name> list
```
![uppstår-lista-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 uppdatera filen för att införa uppstår config */etc/sysconfig/sbd* enligt följande. Uppdatera-filen på **både** noder
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 starta tjänsten pacemaker på den **primära noden** (Nod1)
```
systemctl start pacemaker
```
![Start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Om tjänsten pacemaker *misslyckas*, referera till *Scenario 5: Pacemaker fungerar inte*

## <a name="5---joining-the-cluster"></a>5.   Ansluta till klustret
Det här avsnittet beskrivs på hur du ansluter till noden i klustret.

### <a name="51-add-the-node"></a>5.1 lägger du till noden
Kör följande kommando på **node2** så att nod2 ansluta till klustret.
```
ha-cluster-join
```
Om du får en *fel* under ansluta till klustret finns *Scenario 6: nod 2 Det går inte att ansluta till klustret*.

## <a name="6---validating-the-cluster"></a>6.   Verifiering av kluster

### <a name="61-start-the-cluster-service"></a>6.1 starta klustertjänsten
Kontrollera och du kan också starta klustret för första gången på **både** noder.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 övervaka status
Kör kommandot *crm_mon* så **både** noderna är online. Du kan köra på **någon av noderna** i klustret
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) du kan också logga in hawk för att kontrollera status för klustret *https://<node IP>: 7630*. Standardanvändaren är hacluster och lösenordet är linux. Om det behövs kan du ändra lösenord med hjälp av *passwd* kommando.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurera egenskaper för klustret och resurser 
Det här avsnittet beskrivs hur du konfigurerar klusterresurserna.
I det här exemplet, ställa in följande resurs kan resten konfigureras (vid behov) genom att referera till SUSE HA guiden. Utföra config i **en av noderna** endast. Gör på den primära noden.

- Starttjänsten för kluster
- STONITH enhet
- Virtuell IP-adress


### <a name="71-cluster-bootstrap-and-more"></a>7.1 starttjänsten för klustret och mycket mer
Lägg till klustret starttjänsten. Skapa filen och Lägg till texten som följande:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-bs.txt
```
![CRM konfigurera crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH enhet
Lägg till resurs STONITH. Skapa filen och Lägg till texten som följande.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 virtuella IP-adressen
Lägg till resurs virtuell IP-adress. Skapa filen och Lägg till texten enligt nedan.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Validera resurser

När du kör kommandot *crm_mon*, du kan se de två resurserna.
![crm_mon_command.PNG](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Du kan också se status på *https://<node IP address>: 7630 eller cib/live/tillstånd*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testa redundans-process
Stoppa tjänsten pacemaker på Nod1 och resurser redundans till nod2 för att testa redundans-processen.
```
Service pacemaker stop
```
Stoppa tjänsten pacemaker nu på **node2** och resurser som flyttas över till **Nod1**

**Innan du redundans**
![innan failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**efter redundans**
![efter failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM mon efter failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Felsökning
Det här avsnittet beskrivs några felscenarier, vilket kan uppstå under installationen. Du kan inte nödvändigtvis att få dessa problem.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: Klusternoden inte online
Om någon av noderna inte visas i hanteraren för redundanskluster, kan du prova följande om du vill ta den online.

Starta tjänsten iSCSI
```
service iscsid start
```

Och du ska nu kunna logga in på iSCSI-noden
```
iscsiadm -m node -l
```
Utdata som förväntas ser ut som följande
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenario 2: yast2 visar inte grafisk vy
Skärmen yast2 grafiska används för att ställa in kluster med hög tillgänglighet i det här dokumentet. Om yast2 inte öppna med grafiska fönstret som visas och utlösa Qt fel, följer du anvisningarna som följande. Om det öppnas med grafiska fönster, kan du hoppa över stegen.

**Fel**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Utdata som förväntas**

![yast-kontroll-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Om yast2 inte öppnas med grafisk vy, följer du följande steg.

Installera de paket som behövs. Du måste vara inloggad som användare ”rot” och har SMT ställts in för att ladda ned och installera paket.

Installera paketen med yast > program > programvaruhantering > beroenden > alternativet ”installation rekommenderas paket...”. Följande skärmbild visar förväntade skärmar.
>[!NOTE]
>Du måste utföra stegen på båda noderna, så att du kan komma åt yast2 grafisk vy från båda noderna.

![yast sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Under beroenden, väljer du ”installera rekommenderas Packages- ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Granska ändringarna och tryck på OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Paketet installationen fortsätter ![yast utför installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klicka på Nästa

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klicka på Slutför

Du måste också installera libqt4 och libyui qt paket.
```
zypper -n install libqt4
```
![zypper-installation-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-installation-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 ska nu kunna öppna grafisk vy nu som visas här.
![yast2-kontroll-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: yast2 har inte hög tillgänglighet alternativ
Du måste installera ytterligare paket för alternativet hög tillgänglighet som ska visas på yast2 kontrollcenter.

Med hjälp av Yast2 > program > programvaruhantering > Välj följande mönster

- Grundläggande SAP HANA-server
- C/C++-kompilatorn och verktyg
- Hög tillgänglighet
- SAP-programserver bas

Följande skärmbild visar stegen för att installera mönster.

Med hjälp av yast2 > program > programvaruhantering

![yast2-kontroll-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Välj mönster

![yast pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klicka på **acceptera**

![yast ändras packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klicka på **fortsätta**

![yast2 utför installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klicka på **nästa** när installationen är klar

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: HANA installationen misslyckas med gcc sammansättningar fel
HANA-installationen misslyckas med följande fel.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Om du vill åtgärda problemet måste du installera bibliotek (libgcc_sl och libstdc ++ 6) enligt följande.

![zypper-installation-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: Pacemaker fungerar inte

Följande problem uppstod när den pacemaker starten.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Åtgärda det genom att ta bort följande rad från filen */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.PNG](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: Nod 2 som det går inte att ansluta till klustret

När ansluts nod2 till det befintliga klustret med hjälp av *ha kluster-koppling* kommandot följande fel uppstod.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![hög tillgänglighet-kluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Om du vill åtgärda, kör du följande på båda noderna

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![SSH-keygen-Nod1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![SSH-keygen-nod 2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Efter den föregående korrigeringsfilen ska nod2 hämta läggas till i klustret

![hög tillgänglighet-kluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Allmän dokumentation
Du hittar mer information om SUSE HA installation i följande artiklar: 

- [Prestanda för SAP HANA SR optimerade Scenario](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Storage-baserade avgränsningar](https://www.suse.com/documentation/sle-ha-2/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blogg - Pacemaker kluster för SAP HANA - del 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blogg - Pacemaker kluster för SAP HANA - del 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)