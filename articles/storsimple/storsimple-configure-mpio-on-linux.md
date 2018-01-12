---
title: "Konfigurera MPIO på StorSimple Linux-värd | Microsoft Docs"
description: "Konfigurera MPIO på StorSimple som är ansluten till en Linux-värd som kör CentOS 6.6"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 2fbae15c1c6a9ec886f57f9df903612ae10d8e12
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurera MPIO på en StorSimple-värd som kör CentOS
Den här artikeln beskrivs de steg som krävs för att konfigurera flera sökvägar I/O (MPIO) på värdservern Centos 6.6. Värdservern är ansluten till din Microsoft Azure StorSimple-enhet för hög tillgänglighet via iSCSI-initierare. Det beskrivs i detalj automatisk identifiering av multipath enheter och de specifika inställningarna endast för StorSimple-volymer.

Den här proceduren gäller för alla modeller av StorSimple 8000-serien enheter.

> [!NOTE]
> Den här proceduren kan inte användas för en StorSimple-enhet för molnet. Mer information finns i så här konfigurerar du värdservrar för din enhet i molnet.


## <a name="about-multipathing"></a>Om flera sökvägar
MPIO-funktionen kan du konfigurera flera i/o-sökvägar mellan en värdserver och en lagringsenhet. Dessa i/o-sökvägar är fysiska SAN-anslutningar som kan innehålla olika kablar, växlar, nätverksgränssnitt och domänkontrollanter. Multipathing aggregerar i/o-sökvägar, om du vill konfigurera en ny enhet som är associerad med alla sammanställda sökvägar.

Syftet med flera sökvägar är dubbla:

* **Hög tillgänglighet**: den ger en alternativ sökväg om någon del av i/o-sökväg (t.ex en kabel växel, nätverksgränssnittet eller domänkontrollant) misslyckas.
* **Belastningsutjämning**: beroende på konfigurationen av lagringsenheten är det kan förbättra prestanda genom att identifiera belastningen på i/o-sökvägar och dynamiskt ombalansering dessa belastning.

### <a name="about-multipathing-components"></a>Om MPIO-komponenter
Flera sökvägar i Linux består av kernel-komponenter och användaren utrymme komponenter enligt tabellen nedan.

* **Kernel**: huvudkomponenten är den *enheten mapper* som dras om i/o och stöd för växling vid fel för sökvägar och sökvägen grupper.

* **Användaren kan**: dessa är *multipath verktyg* som hanterar multipathed enheter genom att uppmana enheten-multipath modulen för mappning av vad du gör. Verktygen består av:
   
   * **Multipath**: Visar och konfigurerar multipathed enheterna.
   * **Multipathd**: daemon som körs multipath och övervakar sökvägar.
   * **Devmap namn**: ger en beskrivande enhetsnamn för udev för devmaps.
   * **Kpartx**: mappar linjär devmaps till enheten partitioner för att multipath maps partitionable.
   * **Multipath.conf**: konfigurationsfilen för flera sökvägar daemon som används för att skriva över inbyggda configuration-tabellen.

### <a name="about-the-multipathconf-configuration-file"></a>Om konfigurationsfilen multipath.conf
Konfigurationsfilen `/etc/multipath.conf` gör många av funktionerna för flera sökvägar kan konfigureras av användaren. Den `multipath` kommandot och daemonen kernel `multipathd` använda informationen i den här filen. Filen samråd endast under konfigurationen av multipath enheter. Kontrollera att alla ändringar innan du kör den `multipath` kommando. Om du ändrar filen efteråt behöver du stoppa och starta multipathd igen för att ändringarna ska börja gälla.

Multipath.conf har fem avsnitt:

- **Nivån systemstandard** *(standardinställningen)*: du kan åsidosätta standardvärdena för nivån.
- **Övriga enheter** *(svartlistat)*: du kan ange en lista över enheter som inte kan styras av enheten mapper.
- **Svartlista undantag** *(blacklist_exceptions)*: du kan identifiera specifika enheter ska behandlas som multipath enheter även om anges i svartlistat.
- **Specifika inställningar för lagring controller** *(enheter)*: du kan ange inställningar som tillämpas på enheter som har leverantör och produktinformation.
- **Specifika Enhetsinställningar** *(multipaths)*: du kan använda det här avsnittet för att finjustera konfigurationsinställningarna för enskilda LUN.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurera MPIO på StorSimple som är anslutna till Linux-värd
En StorSimple-enhet som är ansluten till en Linux-värd kan konfigureras för hög tillgänglighet och belastningsutjämning. Till exempel om Linux-värd har två gränssnitt som är anslutna till SAN och enheten har två gränssnitt som är anslutna till SAN-nätverk så att dessa gränssnitt i samma undernät, ska sedan det finnas 4 sökvägar. Men om varje gränssnitt för DATA på enheten och värd-gränssnittet är i ett annat IP-undernät (och inte dirigerbara) kan blir endast 2 sökvägar tillgängliga. Du kan konfigurera MPIO för att automatiskt identifiera alla tillgängliga sökvägar, välja en algoritm för belastningsutjämning för dessa sökvägar, tillämpa specifika konfigurationsinställningar för endast StorSimple-volymer, och sedan aktivera och verifiera MPIO.

Följande procedur beskriver hur du konfigurerar MPIO när en StorSimple-enhet med två nätverksgränssnitt som är ansluten till en värd med två nätverksgränssnitt.

## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet beskrivs konfigurationskraven för CentOS server och din StorSimple-enhet.

### <a name="on-centos-host"></a>På CentOS värden
1. Kontrollera att värddatorn CentOS har 2 nätverksgränssnitt som är aktiverad. Ange:
   
    `ifconfig`
   
    I följande exempel visas utdata när två nätverksgränssnitt (`eth0` och `eth1`) finns på värden.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
2. Installera *verktyg för iSCSI-initieraren-webbplatsuppgradering* på CentOS servern. Utför följande steg för att installera *verktyg för iSCSI-initieraren-webbplatsuppgradering*.
   
   1. Logga in som `root` till CentOS-värden.
   2. Installera den *verktyg för iSCSI-initieraren-webbplatsuppgradering*. Ange:
      
       `yum install iscsi-initiator-utils`
   3. Efter den *verktyg för iSCSI-initieraren-webbplatsuppgradering* är korrekt installerat, starta iSCSI-tjänsten. Ange:
      
       `service iscsid start`
      
       Vid tillfällen `iscsid` inte kan starta och `--force` alternativet kan behövas
   4. Använd för att säkerställa att iSCSI-initieraren är aktiverad under starten av `chkconfig` kommando för att aktivera tjänsten.
      
       `chkconfig iscsi on`
   5. Kontrollera att den har korrekt installationsprogrammet kör du kommandot:
      
       `chkconfig --list | grep iscsi`
      
       Ett exempel på utdata visas nedan.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       Du kan se att iSCSI-miljö ska köras på starttiden på Kör nivå 2, 3, 4 och 5 från exemplet ovan.
3. Installera *enhet-mapper-multipath*. Ange:
   
    `yum install device-mapper-multipath`
   
    Installationen startar. Typen **Y** att fortsätta när du uppmanas att bekräfta.

### <a name="on-storsimple-device"></a>På StorSimple-enhet
Din StorSimple-enhet bör ha:

* Minst två gränssnitt som är aktiverade för iSCSI. Utför följande steg för att kontrollera att två gränssnitt som är iSCSI-aktiverade på din StorSimple-enhet i den klassiska Azure-portalen för din StorSimple-enhet:
  
  1. Logga in på den klassiska portalen för din StorSimple-enhet.
  2. Välj din StorSimple Manager-tjänsten, klicka på **enheter** och välj den specifika StorSimple-enheten. Klicka på **konfigurera** och kontrollera inställningar för nätverksgränssnitt. En skärmbild med två iSCSI-aktiverade nätverkskort visas nedan. Här DATA 2 och DATA 3, både 10 GbE gränssnitt har aktiverats för iSCSI.
     
      ![MPIO StorsSimple DATA 2-konfiguration](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      I den **konfigurera** sidan
     
     1. Se till att båda nätverksgränssnitt är iSCSI-aktiverade. Den **iSCSI-aktiverade** fält ska anges **Ja**.
     2. Se till att nätverksgränssnitt har samma hastighet måste båda vara 1 GbE- eller 10 GbE.
     3. Notera IPv4-adresser för iSCSI-aktiverade gränssnitt och spara för senare användning på värden.
* ISCSI-gränssnitt på StorSimple-enheten ska kunna nås från CentOS-servern.
      Om du vill kontrollera detta, som du behöver ange IP-adresserna för din StorSimple iSCSI-aktiverade nätverkskort på värdservern. De kommandon som används och motsvarande utdata med DATA2 (10.126.162.25) och DATA3 (10.126.162.26) visas nedan:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Maskinvarukonfiguration
Vi rekommenderar att du ansluter två iSCSI-nätverksgränssnitt i separata sökvägar för redundans. Bilden nedan visar den rekommenderade maskinvarukonfigurationen för hög tillgänglighet och belastningsutjämning MPIO för din CentOS server och StorSimple-enhet.

![MPIO maskinvarukonfiguration för StorSimple till Linux-värd](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Som visas i föregående bild:

* StorSimple-enheten är i ett aktivt-passivt konfiguration med två domänkontrollanter.
* Två SAN-switchar är anslutna till din styrenheter.
* Två iSCSI-initierarna har aktiverats på din StorSimple-enhet.
* Två nätverksgränssnitt är aktiverade på CentOS-värden.

Konfigurationen ovan kommer att ge 4 separat sökvägar mellan enheten och värden om data från värdar och gränssnitten är dirigerbara.

> [!IMPORTANT]
> * Vi rekommenderar att du inte blandar 1 GbE och 10 GbE-nätverkskort för flera sökvägar. När du använder två nätverksgränssnitt, ska båda gränssnitten vara identiska typen.
> * På din StorSimple-enhet DATA0, fil1, DATA4 och DATA5 är 1 GbE-gränssnitt DATA2 och DATA3 är 10 GbE-nätverkskort. |
> 
> 

## <a name="configuration-steps"></a>Konfigurationssteg
Konfigurationssteg för flera sökvägar innebär konfigurering av de tillgängliga sökvägarna för automatisk identifiering, ange algoritmen utjämning ska användas, aktivera MPIO och slutligen verifierar konfigurationen. Var och en av dessa steg beskrivs i detalj i följande avsnitt.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Steg 1: Konfigurera MPIO för automatisk identifiering
Stöd för MPIO-enheter kan identifieras och konfigureras automatiskt.

1. Initiera `/etc/multipath.conf` fil. Ange:
   
     `mpathconf --enable`
   
    Ovanstående kommando skapar en `sample/etc/multipath.conf` fil.
2. Starta flera sökvägar. Ange:
   
    `service multipathd start`
   
    Följande utdata visas:
   
    `Starting multipathd daemon:`
3. Aktivera automatisk identifiering av multipaths. Ange:
   
    `mpathconf --find_multipaths y`
   
    Detta kommer att ändra standardinställningar för din `multipath.conf` enligt nedan:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Steg 2: Konfigurera MPIO för StorSimple-volymer
Som standard alla enheter är svarta i filen multipath.conf och ska kringgås. Du behöver skapa svartlistat undantag för att tillåta flera sökvägar för volymer från StorSimple-enheter.

1. Redigera den `/etc/mulitpath.conf` filen. Ange:
   
    `vi /etc/multipath.conf`
2. Leta upp avsnittet blacklist_exceptions i filen multipath.conf. Din StorSimple-enhet måste anges som ett svartlistat undantag i det här avsnittet. Du kan ta bort kommentarerna relevanta rader i den här filen för att ändra det som visas nedan (Använd endast specifika modell för den enhet du använder):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Steg 3: Konfigurera MPIO för resursallokering
Belastningsutjämning algoritmen använder alla tillgängliga multipaths till den aktiva styrenheten i ett balanserat, resursallokering sätt.

1. Redigera den `/etc/multipath.conf` filen. Ange:
   
    `vi /etc/multipath.conf`
2. Under den `defaults` anger den `path_grouping_policy` till `multibus`. Den `path_grouping_policy` anger standardsökvägen gruppering principen ska gälla för ospecificerade multipaths. Avsnittet standardinställningar ser ut som nedan.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> De vanligaste värdena för `path_grouping_policy` omfattar:
> 
> * redundans = 1 sökväg per prioritet grupp
> * multibus = alla giltiga sökvägar i grupp 1 prioritet
> 
> 

### <a name="step-4-enable-multipathing"></a>Steg 4: Aktivera MPIO
1. Starta om den `multipathd` daemon. Ange:
   
    `service multipathd restart`
2. Utdata blir enligt nedan:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Steg 5: Kontrollera MPIO
1. Kontrollera att iSCSI-anslutning upprättas med StorSimple-enheten på följande sätt:
   
   a. Identifiera din StorSimple-enhet. Ange:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Utdata när IP-adress för DATA0 är 10.126.162.25 och port 3260 öppnas på StorSimple-enhet för utgående iSCSI-trafik är enligt nedan:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Kopiera IQN för din StorSimple-enhet `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, från föregående utdata.

   b. Ansluta till enheten med mål IQN. StorSimple-enhet är iSCSI-målet här. Ange:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    I följande exempel visas med ett mål IQN av `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. Utdata visar att du har anslutit till två iSCSI-aktiverade nätverksgränssnitt på enheten.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Om du ser en enda värdgränssnitt och här två sökvägar, måste du aktivera båda gränssnitten på iSCSI-värden. Du kan följa den [detaljerade instruktioner i dokumentationen för Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

2. En volym är exponerad för CentOS servern från StorSimple-enhet. Mer information finns i [steg 6: skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) via Azure portal på StorSimple-enheten.

3. Kontrollera de tillgängliga sökvägarna. Ange:

      ```
      multipath –l
      ```

      I följande exempel visas utdata för två nätverkskort på en StorSimple-enhet som är ansluten till en enda värd nätverksgränssnitt med två tillgängliga sökvägar.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Felsöka MPIO
Det här avsnittet innehåller några nyttiga tips om du stöter på problem vid konfiguration av MPIO.

FRÅGOR. Visas inte ändringarna i `multipath.conf` fil börjar gälla.

A. Om du har gjort ändringar i `multipath.conf` fil, du måste starta om tjänsten MPIO. Ange följande kommando:

    service multipathd restart

FRÅGOR. Jag har aktiverat två nätverksgränssnitt på StorSimple-enheten och två nätverkskort på värden. När jag listar de tillgängliga sökvägarna visas bara två sökvägar. Det förväntas finns fyra tillgängliga sökvägar.

A. Se till att de två sökvägarna är i samma undernät och dirigerbara. Om nätverksgränssnitten som finns på olika VLAN och inte dirigeras visas bara två sökvägar. Ett sätt att kontrollera detta är att se till att du kan nå båda värden gränssnitten från ett nätverksgränssnitt på StorSimple-enheten. Du behöver [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md) som den här kontrollen kan bara göras via en supportsession.

FRÅGOR. När jag Listar tillgängliga sökvägar syns inte några utdata.

A. Normalt inte ser några multipathed sökvägar tyder på problem med flera sökvägar daemon och det är troligen att alla problem här ligger i den `multipath.conf` filen.

Det kan även vara värt att kontrollera att du verkligen kan se några diskar efter anslutning till målet, eftersom inget svar från multipath listor kan också innebära du inte har några diskar.

* Använd följande kommando för att skanna SCSI-bussen:
  
    `$ rescan-scsi-bus.sh `(en del av sg3_utils paketet)
* Skriv följande kommandon:
  
    `$ dmesg | grep sd*`
     
     Eller
  
    `$ fdisk –l`
  
    Dessa kommer att returnera information om nyligen tillagda diskar.
* Använd följande kommandon för att avgöra om det är en StorSimple-disk:
  
    `cat /sys/block/<DISK>/device/model`
  
    Detta returnerar en sträng som avgör om det är en StorSimple-disk.

En mindre troligt men möjlig orsak kan också vara inaktuella iscsid pid. Använd följande kommando för att logga ut från iSCSI-sessioner:

    iscsiadm -m node --logout -p <Target_IP>

Upprepa det här kommandot för alla anslutna nätverkskort på iSCSI-mål, vilket är din StorSimple-enhet. När du har loggat från iSCSI-sessioner, kan du använda iSCSI-målet IQN för att återupprätta iSCSI-session. Ange följande kommando:

    iscsiadm -m node --login -T <TARGET_IQN>


FRÅGOR. Jag är inte säker på om enheten är godkända.

A. Använd felsökning interaktiva kommandot för att kontrollera om enheten är godkända:

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Mer information finns på [använda felsökning interaktiva kommandot för flera sökvägar](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Lista över användbara kommandon
| Typ | Kommando | Beskrivning |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Starta iSCSI-tjänsten |
| &nbsp; |`service iscsid stop` |Stoppa tjänsten iSCSI |
| &nbsp; |`service iscsid restart` |Starta om iSCSI-tjänsten |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Identifiera tillgängliga mål på den angivna adressen |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Logga in på iSCSI-målet |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Logga ut från iSCSI-målet |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Namn på iSCSI-initierare |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Kontrollera tillståndet för iSCSI-session och volymen som identifieras på värden |
| &nbsp; |`iscsi –m session` |Visar alla iSCSI-sessioner mellan värden och StorSimple-enhet |
|  | | |
| **Flera sökvägar** |`service multipathd start` |Starta daemon för flera sökvägar |
| &nbsp; |`service multipathd stop` |Stoppa multipath daemon |
| &nbsp; |`service multipathd restart` |Starta om multipath daemon |
| &nbsp; |`chkconfig multipathd on` </br> ELLER </br> `mpathconf –with_chkconfig y` |Aktivera multipath daemon att starta vid start |
| &nbsp; |`multipathd –k` |Starta den interaktiva för felsökning |
| &nbsp; |`multipath –l` |Lista över multipath anslutningar och enheter |
| &nbsp; |`mpathconf --enable` |Skapa en exempelfil mulitpath.conf i`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Nästa steg
Som du konfigurerar MPIO på Linux-värd, kan du också behöva referera till följande CentoS 6.6 dokument:

* [Hur du konfigurerar MPIO på CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Guide för Linux-utbildning](http://linux-training.be/files/books/LinuxAdm.pdf)

