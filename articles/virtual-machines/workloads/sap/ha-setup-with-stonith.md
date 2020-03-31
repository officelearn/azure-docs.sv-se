---
title: Hög tillgänglighet konfigureras med STONITH för SAP HANA på Azure (stora instanser)| Microsoft-dokument
description: Upprätta hög tillgänglighet för SAP HANA på Azure (stora instanser) i SUSE med hjälp av STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4060dbe936af8ff1f9dd8c958f64834cb06525de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77615088"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Konfigurera hög tillgänglighet i SUSE med hjälp av STONITH
Det här dokumentet innehåller detaljerade steg för steg-instruktioner för att ställa in operativsystemet Hög tillgänglighet på SUSE-operativsystemet med STONITH-enheten.

**Ansvarsfriskrivning:** *Den här guiden härleds genom att testa installationen i Microsoft HANA Large Instances-miljön, som fungerar. Eftersom Microsoft Service Management Team for HANA Large Instances inte stöder operativsystem kan du behöva kontakta SUSE för ytterligare felsökning eller förtydligande på operativsystemets lager. Microsofts servicehanteringsteam konfigurerar STONITH-enhet och stöder och kan vara inblandade för felsökning av STONITH-enhetsproblem.*
## <a name="overview"></a>Översikt
För att ställa in hög tillgänglighet med SUSE-klustring måste följande förutsättningar uppfyllas.
### <a name="pre-requisites"></a>Förutsättningar
- Stora HANA-instanser etableras
- Operativsystemet är registrerat
- HANA Stora instanser servrar är anslutna till SMT-server för att få patchar / paket
- Operativsystemet har senaste korrigeringsfiler installerade
- NTP (tidsserver) är konfigurerat
- Läsa och förstå den senaste versionen av SUSE-dokumentation om HA-inställningar

### <a name="setup-details"></a>Information om inställningar
Den här guiden använder följande inställningar:
- Operativsystem: SLES 12 SP1 för SAP
- Stora HANA-förekomster: 2xS192 (fyra uttag, 2 TB)
- HANA Version: HANA 2.0 SP1
- Servernamn: sapprdhdb95 (nod1) och sapprdhdb96 (nod2)
- STONITH-enhet: iSCSI-baserad STONITH-enhet
- NTP-uppsättning på en av HANA Large Instance-noder

När du konfigurerar stora HANA-instanser med HSR kan du begära att Microsoft Service Management-teamet konfigurerar STONITH. Om du redan är en befintlig kund som har hana stora instanser etablerade och behöver STONITH-enhet konfigurerad för dina befintliga blad, måste du ange följande information till Microsoft Service Management-teamet i formuläret för servicebegäran (SRF). Du kan begära SRF-formulär via Technical Account Manager eller din Microsoft-kontakt för Å-förekomst av HANA-instans. De nya kunderna kan begära STONITH-enhet vid etableringstillfället. Indata är tillgängliga i formuläret etableringsbegäran.

- Servernamn och server-IP-adress (till exempel myhanaserver1, 10.35.0.1)
- Plats (till exempel US East)
- Kundnamn (till exempel Microsoft)
- SID - HANA Systemidentifierare (till exempel H11)

När STONITH-enheten har konfigurerats ger Microsoft Service Management-teamet dig SBD-enhetens namn och IP-adress för iSCSI-lagringen, som du kan använda för att konfigurera STONITH-konfigurationen. 

Om du vill ställa in på HA med STONITH måste följande steg följas:

1.  Identifiera SBD-enheten
2.  Initiera SBD-enheten
3.  Konfigurera klustret
4.  Ställa in Softdog Watchdog
5.  Ansluta noden till klustret
6.  Verifiera klustret
7.  Konfigurera resurserna till klustret
8.  Testa redundansprocessen

## <a name="1---identify-the-sbd-device"></a>1. Identifiera SBD-enheten
I det här avsnittet beskrivs hur du tar reda på SBD-enheten för din konfiguration efter att Microsofts servicehanteringsteam har konfigurerat STONITH. **Det här avsnittet gäller endast för den befintliga kunden**. Om du är en ny kund tillhandahåller Microsofts servicehanteringsteam SBD-enhetsnamn till dig och du kan hoppa över det här avsnittet.

1.1 Ändra */etc/iscsi/initiatorname.isci* till 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft-tjänsthantering tillhandahåller den här strängen. Ändra filen på **båda** noderna, men nodnumret är olika på varje nod.

![initieraorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Ändra */etc/iscsi/iscsid.conf*: Ange *nod.session.timeo.replacement_timeout=5* och *node.startup = automatisk*. Ändra filen på **båda** noderna.

1.3 Kör identifieringskommandot, den visar fyra sessioner. Kör den på båda noderna.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Kör kommandot för att logga in på iSCSI-enheten, den visar fyra sessioner. Kör den på **båda** noderna.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Kör omsökningsskriptet: *rescan-scsi-bus.sh*.  Det här skriptet visar de nya diskar som skapats åt dig.  Kör den på båda noderna. Du bör se ett LUN-tal som är större än noll (till exempel: 1, 2 etc.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 För att få enhetens namn köra kommandot *fdisk-l*. Kör den på båda noderna. Välj enheten med storleken **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Initiera SBD-enheten

2.1 Initiera SBD-enheten på **båda** noderna

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Kontrollera vad som har skrivits till enheten. Gör det på **båda** noderna

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Konfigurera klustret
I det här avsnittet beskrivs stegen för att ställa in SUSE HA-klustret.
### <a name="31-package-installation"></a>3.1 Paketinstallation
3.1.1 Kontrollera att ha_sles och SAPHanaSR-doc-mönstren är installerade. Om den inte är installerad installerar du dem. Installera det på **båda** noderna.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Ställa in klustret
3.2.1 Du kan antingen använda *kommandot ha-cluster-init* eller använda yast2-guiden för att konfigurera klustret. I det här fallet används yast2-guiden. Du utför det här steget **endast på den primära noden**.

Följ yast2> hög tillgänglighet > Cluster ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klicka på **Avbryt** eftersom halk2-paketet redan är installerat.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klicka på **Fortsätt**

Förväntat värde=Antal noder som har distribuerats (i det här fallet ![2)](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Klicka på **Nästa**
![yast-cluster-configure-csync2.png Lägg till nodnamn och klicka sedan på "Lägg till föreslagna filer"

Klicka på "Aktivera csync2 ON"

Klicka på "Generera pre-shared-keys", det visas nedan popup

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klicka på **OK**

Autentiseringen utförs med IP-adresserna och fördelade nycklar i Csync2. Nyckelfilen genereras med csync2 -k /etc/csync2/key_hagroup. Filen key_hagroup ska kopieras till alla medlemmar i klustret manuellt när den har skapats. **Kontrollera att filen kopieras från nod 1 till nod2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klicka på **Nästa**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

I standardalternativet var uppstart av, ändra den till "på" så pacemaker startas vid uppstart. Du kan göra valet baserat på dina inställningskrav.
Klicka på **Nästa** och klusterkonfigurationen är klar.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Ställa in Softdog Watchdog
I det här avsnittet beskrivs konfigurationen av watchdog (softdog).

4.1 Lägg till följande rad i */etc/init.d/boot.local* på **båda** noderna.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Uppdatera filen */etc/sysconfig/sbd* på **båda** noderna enligt följande:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Ladda kärnmodulen på **båda** noderna genom att köra följande kommando
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Kontrollera och se till att softdog körs som följande på **båda** noderna:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Starta SBD-enheten på **båda** noderna
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Testa SBD-demonen på **båda** noderna. Du ser två poster när du har konfigurerat dem på **båda** noderna
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Skicka ett testmeddelande till **en** av dina noder
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 På den **andra** noden (nod2) kan du kontrollera meddelandestatus
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 För att anta sbd config, uppdatera filen */ etc / sysconfig /sbd* enligt följande. Uppdatera filen på **båda** noderna
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Starta pacemakertjänsten på den **primära noden** (nod1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Om pacemakertjänsten *misslyckas*läser du *Scenario 5: Pacemaker-tjänsten misslyckas*

## <a name="5---joining-the-cluster"></a>5. Ansluta sig till klustret
I det här avsnittet beskrivs hur du ansluter noden till klustret.

### <a name="51-add-the-node"></a>5.1 Lägg till noden
Kör följande kommando på **nod2** så att nod2 kan ansluta till klustret.
```
ha-cluster-join
```
Om du får ett *felmeddelande* när du ansluter till klustret läser du *Scenario 6: Nod 2 kan inte ansluta till klustret*.

## <a name="6---validating-the-cluster"></a>6. Validera klustret

### <a name="61-start-the-cluster-service"></a>6.1 Starta klustertjänsten
Om du vill kontrollera och starta klustret för första gången på **båda** noderna.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Övervaka status
Kör kommandot *crm_mon* för att säkerställa att **båda** noderna är online. Du kan köra den på **någon av noderna** i klustret
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) Du kan också logga in på hök för att kontrollera klusterstatus *https://\<nod IP->:7630*. Standardanvändaren är hacluster och lösenordet är Linux. Om det behövs kan du ändra lösenordet med *kommandot passwd.*

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurera klusteregenskaper och resurser 
I det här avsnittet beskrivs stegen för att konfigurera klusterresurserna.
I det här exemplet ställer du in följande resurs, resten kan konfigureras (om det behövs) genom att referera till SUSE HA-guiden. Utför konfigurationen endast i **en av noderna.** Gör på primär nod.

- Kluster bootstrap
- STONITH-enhet
- Den virtuella IP-adressen


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Kluster bootstrap och mer
Lägg till klusterkängar. Skapa filen och lägg till texten enligt följande:
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
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH-enhet
Lägg till resurs STONITH. Skapa filen och lägg till texten enligt följande.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Lägg till konfigurationen i klustret.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 Den virtuella IP-adressen
Lägg till virtuell resurs-IP. Skapa filen och lägg till texten enligt nedan.
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

### <a name="74-validate-the-resources"></a>7.4 Validera resurserna

När du kör kommandot *crm_mon*kan du se de två resurserna där.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Du kan också se statusen på *https://\<nod-IP-adress>:7630/cib/live/state*

![hawlk-status-sida.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testa redundansprocessen
Om du vill testa redundansprocessen stoppar du pacemakertjänsten på nod1 och resurserna redundans till nod2.
```
Service pacemaker stop
```
Stoppa nu pacemakertjänsten på **nod2** och resurser gick inte över till **nod1**

**Före redundans**  
![Före failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Efter redundans**  
![efter failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mån-efter-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Felsökning
I det här avsnittet beskrivs de få felscenarier som kan påträffas under installationen. Du kanske inte nödvändigtvis står inför dessa frågor.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: Klusternod är inte online
Om någon av noderna inte visas online i klusterhanteraren kan du prova att följa för att ansluta den.

Starta iSCSI-tjänsten
```
service iscsid start
```

Och nu ska du kunna logga in på den iSCSI-noden
```
iscsiadm -m node -l
```
Den förväntade produktionen ser ut att följa
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
Den grafiska yast2-skärmen används för att konfigurera klustret Hög tillgänglighet i det här dokumentet. Om yast2 inte öppnas med det grafiska fönstret som visas och kasta Qt-fel utför du stegen enligt följande. Om den öppnas med det grafiska fönstret kan du hoppa över stegen.

**Fel**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Förväntad utflöde**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Om yast2 inte öppnas med den grafiska vyn följer du stegen nedan.

Installera de paket som behövs. Du måste vara inloggad som användarens "root" och ha SMT konfigurerat för att hämta/installera paketen.

Om du vill installera paketen använder du yast>Software>Software Management>Beroenden> alternativet "Installera rekommenderade paket...". Följande skärmbild illustrerar de förväntade skärmarna.
>[!NOTE]
>Du måste utföra stegen på båda noderna, så att du kan komma åt den grafiska yast2-vyn från båda noderna.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Under Beroenden väljer du "Installera ![rekommenderade paket" yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Granska ändringarna och tryck på OK

![yast (yast)](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Paketinstallation ![fortsätter yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klicka på Nästa

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klicka på Slutför

Du måste också installera libqt4- och libyui-qt-paketen.
```
zypper -n install libqt4
```
![zypper-installera-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 bör kunna öppna den grafiska vyn nu som visas här.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: yast2 inte hög tillgänglighet alternativ
För att alternativet Hög tillgänglighet ska vara synligt på yast2-kontrollcentret måste du installera ytterligare paket.

Använda Hantering av Yast2>Software>Software>Välj följande mönster

- SAP HANA-serverbas
- C/C++ Kompilator och verktyg
- Hög tillgänglighet
- Serverbas för SAP-program

Följande skärm visar stegen för att installera mönstren.

Använda yast2 > Software > Software Management

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Markera mönstren

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klicka på **Acceptera**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klicka på **Fortsätt**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klicka på **Nästa** när installationen är klar

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: HANA-installationen misslyckas med gcc-sammansättningar fel
HANA-installationen misslyckas med följande fel.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

För att åtgärda problemet måste du installera bibliotek (libgcc_sl och libstdc++6) enligt följande.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: Pacemaker-tjänsten misslyckas

Följande problem uppstod under pacemakertjänstens start.

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
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
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

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: Nod 2 kan inte ansluta till klustret

När noden 2 anslöts till det befintliga klustret med kommandot *ha-cluster-join* uppstod följande fel.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Åtgärda, kör följande på båda noderna

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-nod1. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-nod2. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Efter föregående korrigering ska nod2 läggas till i klustret

![ha-kluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Allmän dokumentation
Du hittar mer information om SUSE HA-inställningar i följande artiklar: 

- [SAP HANA SR Prestandaoptimerat scenario](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Lagringsbaserade stängsel](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blogg - Använda Pacemaker Cluster för SAP HANA- Del 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blogg - Använda Pacemaker Cluster för SAP HANA- Del 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
