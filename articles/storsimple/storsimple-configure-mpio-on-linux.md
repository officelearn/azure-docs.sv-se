---
title: Konfigurera MPIO på StorSimple Linux-värd
description: Konfigurera MPIO på StorSimple som är ansluten till en Linux-värd som kör CentOS 6.6
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278366"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurera MPIO på en StorSimple-värd som kör CentOS
I den här artikeln beskrivs de steg som krävs för att konfigurera MPIO (Multipathing IO) på din Centos 6.6-värdserver. Värdservern är ansluten till din Microsoft Azure StorSimple-enhet för hög tillgänglighet via iSCSI-initierare. Den beskriver i detalj den automatiska identifieringen av multipath-enheter och den specifika inställningen endast för StorSimple-volymer.

Den här proceduren gäller för alla modeller av Enheter i StorSimple 8000-serien.

> [!NOTE]
> Den här proceduren kan inte användas för en StorSimple Cloud Appliance. Mer information finns i hur du konfigurerar värdservrar för molninstallationen.


## <a name="about-multipathing"></a>Om multipathing
Med multipathing-funktionen kan du konfigurera flera I/O-sökvägar mellan en värdserver och en lagringsenhet. Dessa I/O-sökvägar är fysiska SAN-anslutningar som kan innehålla separata kablar, växlar, nätverksgränssnitt och styrenheter. Multipathing sammanställer I/O-sökvägarna för att konfigurera en ny enhet som är associerad med alla aggregerade sökvägar.

Syftet med multipathing är tvåfaldigt:

* **Hög tillgänglighet**: Det ger en alternativ sökväg om någon del av I/O-sökvägen (till exempel en kabel, växel, nätverksgränssnitt eller styrenhet) misslyckas.
* **Belastningsutjämning**: Beroende på lagringsenhetens konfiguration kan den förbättra prestanda genom att identifiera belastningar på I/O-banorna och dynamiskt balansera om dessa belastningar.

### <a name="about-multipathing-components"></a>Om multipathing komponenter
Multipathing i Linux består av kärnkomponenter och komponenter i användarutrymme enligt tabell nedan.

* **Kärna**: Huvudkomponenten är *enhetsmapparen* som omdirigerar I/O och stöder redundans för sökvägar och sökvägsgrupper.

* **Användarutrymme:** Det här är *flervägsverktyg* som hanterar flersökta enheter genom att instruera enhetsmappningsmultatormodulen vad du ska göra. Verktygen består av:
   
   * **Multipath**: listar och konfigurerar flersökda enheter.
   * **Multipathd**: demon som kör multipath och övervakar sökvägarna.
   * **Devmap-namn:** ger ett meningsfullt enhetsnamn till udev för devmaps.
   * **Kpartx**: mappar linjära devmaps till enhetspartitioner för att göra multipath-kartor partitionerbara.
   * **Multipath.conf**: konfigurationsfil för multipath daemon som används för att skriva över den inbyggda konfigurationstabellen.

### <a name="about-the-multipathconf-configuration-file"></a>Om konfigurationsfilen multipath.conf
Konfigurationsfilen `/etc/multipath.conf` gör många av multipathing-funktionerna användarkonfigurerbara. Kommandot `multipath` och kernel-demonen `multipathd` använder information som finns i den här filen. Filen konsulteras endast under konfigurationen av multipath-enheterna. Kontrollera att alla ändringar görs innan `multipath` du kör kommandot. Om du ändrar filen efteråt måste du stoppa och starta flera sökvägar igen för att ändringarna ska börja gälla.

Multipath.conf har fem avsnitt:

- **Standardvärden på systemnivå** *(standardvärden):* Du kan åsidosätta standardvärden på systemnivå.
- **Svartlistade enheter** *(svartlista)*: Du kan ange en lista över enheter som inte ska styras av enhetsmapparen.
- **Undantag för svartlista** *(blacklist_exceptions):* Du kan identifiera specifika enheter som ska behandlas som flervägsenheter även om de visas i den svarta listan.
- Specifika inställningar *(enheter för* **lagringsstyrenhet)** : Du kan ange konfigurationsinställningar som ska tillämpas på enheter som har leverantörs- och produktinformation.
- **Enhetsspecifika inställningar** (multipaths) : Du kan använda det här avsnittet för att finjustera konfigurationsinställningarna för enskilda LUN.Device specific settings *(multipaths)*: You can use this section to fine-tune the configuration settings for individual LUNs.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurera multipathing på StorSimple ansluten till Linux-värd
En StorSimple-enhet som är ansluten till en Linux-värd kan konfigureras för hög tillgänglighet och belastningsutjämning. Till exempel, om Linux-värden har två gränssnitt anslutna till SAN och enheten har två gränssnitt anslutna till SAN så att dessa gränssnitt finns på samma undernät, kommer det att finnas 4 sökvägar tillgängliga. Men om varje DATA-gränssnitt på enheten och värdgränssnittet finns på ett annat IP-undernät (och inte dirigerbar), kommer endast två sökvägar att vara tillgängliga. Du kan konfigurera multipathing för att automatiskt identifiera alla tillgängliga sökvägar, välja en belastningsutjämningsalgoritm för dessa sökvägar, tillämpa specifika konfigurationsinställningar för StorSimple-volymer och sedan aktivera och verifiera multipathing.

I följande procedur beskrivs hur du konfigurerar multipathing när en StorSimple-enhet med två nätverksgränssnitt är ansluten till en värd med två nätverksgränssnitt.

## <a name="prerequisites"></a>Krav
I det här avsnittet beskrivs konfigurationsförutsättningarna för CentOS-servern och Din StorSimple-enhet.

### <a name="on-centos-host"></a>På CentOS värd
1. Kontrollera att centOS-värden har 2 nätverksgränssnitt aktiverade. Ange:
   
    `ifconfig`
   
    I följande exempel visas utdata när`eth0` `eth1`två nätverksgränssnitt ( och ) finns på värden.
   
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
1. Installera *iSCSI-initierare-utils* på CentOS-servern. Utför följande steg för att installera *iSCSI-initierare-utils*.
   
   1. Logga in `root` som i din CentOS-värd.
   1. Installera *iSCSI-initieraren-utils*. Ange:
      
       `yum install iscsi-initiator-utils`
   1. När *iSCSI-Initiator-utils* har installerats startar du iSCSI-tjänsten. Ange:
      
       `service iscsid start`
      
       Ibland, `iscsid` kanske inte faktiskt `--force` startar och alternativet kan behövas
   1. Om du vill vara kontrollerar att iSCSI-initieraren är aktiverad under starttiden använder du `chkconfig` kommandot för att aktivera tjänsten.
      
       `chkconfig iscsi on`
   1. Om du vill kontrollera att den har konfigurerats korrekt kör du kommandot:
      
       `chkconfig --list | grep iscsi`
      
       Ett exempel på utdata visas nedan.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       I exemplet ovan kan du se att din iSCSI-miljö körs på starttid på körnivåer 2, 3, 4 och 5.
1. Installera *enhet-mapper-multipath*. Ange:
   
    `yum install device-mapper-multipath`
   
    Installationen startar. Skriv **Y** om du vill fortsätta när du uppmanas att bekräfta.

### <a name="on-storsimple-device"></a>På StorSimple-enhet
Din StorSimple-enhet bör ha:

* Minst två gränssnitt aktiverade för iSCSI. Så här kontrollerar du att två gränssnitt är iSCSI-aktiverade på din StorSimple-enhet och utför följande steg i den klassiska Azure-portalen för din StorSimple-enhet:
  
  1. Logga in på den klassiska portalen för din StorSimple-enhet.
  1. Välj tjänsten StorSimple Manager, klicka på **Enheter** och välj den specifika StorSimple-enheten. Klicka på **Konfigurera** och verifiera nätverksgränssnittsinställningarna. En skärmdump med två iSCSI-aktiverade nätverksgränssnitt visas nedan. Här är DATA 2 och DATA 3, båda 10 GbE-gränssnitt aktiverade för iSCSI.
     
      ![MPIO StorsSimple DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      På sidan **Konfigurera**
     
     1. Kontrollera att båda nätverksgränssnitten är iSCSI-aktiverade. Det **iSCSI-aktiverade** fältet ska anges till **Ja**.
     1. Se till att nätverksgränssnitten har samma hastighet, båda bör vara 1 GbE eller 10 GbE.
     1. Observera IPv4-adresserna för de iSCSI-aktiverade gränssnitten och spara för senare användning på värden.
* ISCSI-gränssnitten på StorSimple-enheten bör kunna nås från CentOS-servern.
      För att kontrollera detta måste du ange IP-adresserna för dina StorSimple iSCSI-aktiverade nätverksgränssnitt på värdservern. De kommandon som används och motsvarande utdata med DATA2 (10.126.162.25) och DATA3 (10.126.162.26) visas nedan:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Maskinvarukonfiguration
Vi rekommenderar att du ansluter de två iSCSI-nätverksgränssnitten på separata sökvägar för redundans. Bilden nedan visar den rekommenderade maskinvarukonfigurationen för hög tillgänglighet och belastningsutjämningsmultning för CentOS-servern och StorSimple-enheten.

![MPIO hårdvara config för StorSimple till Linux värd](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Som visas i föregående figur:

* Din StorSimple-enhet är i en aktiv-passiv konfiguration med två styrenheter.
* Två SAN-switchar är anslutna till enhetskontrollanterna.
* Två iSCSI-initierare är aktiverade på din StorSimple-enhet.
* Två nätverksgränssnitt är aktiverade på CentOS-värden.

Ovanstående konfiguration ger 4 separata sökvägar mellan enheten och värden om värden och datagränssnitten är dirigerbara.

> [!IMPORTANT]
> * Vi rekommenderar att du inte blandar 1 GbE- och 10 GbE-nätverksgränssnitt för multipathing. När du använder två nätverksgränssnitt bör båda gränssnitten vara identiska.
> * På StorSimple-enheten finns DATA0, DATA1, DATA4 och DATA5 1 GbE-gränssnitt medan DATA2 och DATA3 är 10 GbE-nätverksgränssnitt.|
> 
> 

## <a name="configuration-steps"></a>Konfigurationssteg
Konfigurationsstegen för multipathing innebär att konfigurera tillgängliga sökvägar för automatisk identifiering, ange den belastningsutjämningsalgoritm som ska användas, aktivera multipathing och slutligen verifiera konfigurationen. Vart och ett av dessa steg beskrivs i detalj i följande avsnitt.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Steg 1: Konfigurera multipathing för automatisk identifiering
De enheter som stöds av flera sökvägar kan identifieras och konfigureras automatiskt.

1. Initiera `/etc/multipath.conf` filen. Ange:
   
     `mpathconf --enable`
   
    Kommandot ovan skapar `sample/etc/multipath.conf` en fil.
1. Starta multipath-tjänsten. Ange:
   
    `service multipathd start`
   
    Du ser följande utdata:
   
    `Starting multipathd daemon:`
1. Aktivera automatisk identifiering av multipaths. Ange:
   
    `mpathconf --find_multipaths y`
   
    Detta kommer att ändra standardavsnittet i din `multipath.conf` som visas nedan:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Steg 2: Konfigurera multipathing för StorSimple-volymer
Som standard är alla enheter svarta listade i filen multipath.conf och kommer att kringgås. Du måste skapa undantag för svartlista för att tillåta multipathing för volymer från StorSimple-enheter.

1. Redigera `/etc/mulitpath.conf` filen. Ange:
   
    `vi /etc/multipath.conf`
1. Leta reda på blacklist_exceptions avsnittet i filen multipath.conf. Din StorSimple-enhet måste visas som ett undantag för en svart lista i det här avsnittet. Du kan avkommenta relevanta rader i den här filen för att ändra den enligt nedan (använd endast den specifika modellen för den enhet du använder):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Steg 3: Konfigurera multipathing med rundhake
Den här belastningsutjämningsalgoritmen använder alla tillgängliga multipaths till den aktiva styrenheten på ett balanserat, round-robin-sätt.

1. Redigera `/etc/multipath.conf` filen. Ange:
   
    `vi /etc/multipath.conf`
1. Under `defaults` avsnittet ställer `path_grouping_policy` du `multibus`in till . Anger `path_grouping_policy` standardprincipen för bangruppering som ska tillämpas på ospecificerade multipater. Standardavsnittet ser ut som visas nedan.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> De vanligaste värdena är: `path_grouping_policy`
> 
> * redundans = 1 bana per prioritetsgrupp
> * multibus = alla giltiga sökvägar i en prioritetsgrupp
> 
> 

### <a name="step-4-enable-multipathing"></a>Steg 4: Aktivera multipathing
1. Starta `multipathd` om demonen. Ange:
   
    `service multipathd restart`
1. Utgången kommer att vara som visas nedan:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Steg 5: Verifiera multipathing
1. Kontrollera först att iSCSI-anslutningen upprättas med StorSimple-enheten enligt följande:
   
   a. Upptäck din StorSimple-enhet. Ange:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Utdata när IP-adressen för DATA0 är 10.126.162.25 och port 3260 öppnas på StorSimple-enheten för utgående iSCSI-trafik är som visas nedan:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Kopiera IQN för din `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`StorSimple-enhet, från föregående utdata.

   b. Anslut till enheten med mål-IQN. StorSimple-enheten är iSCSI-målet här. Ange:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    I följande exempel visas utdata med `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`ett mål-IQN för . Utdata anger att du har anslutit till de två iSCSI-aktiverade nätverksgränssnitten på enheten.

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

    Om du bara ser ett värdgränssnitt och två sökvägar här måste du aktivera båda gränssnitten på värden för iSCSI. Du kan följa de [detaljerade instruktionerna i Linux-dokumentation](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. En volym exponeras för CentOS-servern från StorSimple-enheten. Mer information finns i [steg 6: Skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) via Azure-portalen på din StorSimple-enhet.

1. Verifiera tillgängliga sökvägar. Ange:

      ```
      multipath -l
      ```

      I följande exempel visas utdata för två nätverksgränssnitt på en StorSimple-enhet som är ansluten till ett enda värdnätverksgränssnitt med två tillgängliga sökvägar.

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

## <a name="troubleshoot-multipathing"></a>Felsöka multipathing
Det här avsnittet innehåller några användbara tips om du stöter på problem under konfigurationen för flera sökvägar.

F. Jag kan inte se `multipath.conf` de ändringar i filen träder i kraft.

A. Om du har gjort `multipath.conf` några ändringar i filen måste du starta om tjänsten för flervalssökning. Ange följande kommando:

    service multipathd restart

F. Jag har aktiverat två nätverksgränssnitt på StorSimple-enheten och två nätverksgränssnitt på värden. När jag listar de tillgängliga sökvägarna ser jag bara två sökvägar. Jag förväntade mig att se fyra tillgängliga vägar.

A. Kontrollera att de två banorna finns i samma undernät och dirigerbara. Om nätverksgränssnitten finns på olika vLAN och inte är dirigerbara visas bara två sökvägar. Ett sätt att verifiera detta är att se till att du kan nå båda värdgränssnitten från ett nätverksgränssnitt på StorSimple-enheten. Du måste [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) eftersom den här verifieringen endast kan göras via en supportsession.

F. När jag listar tillgängliga sökvägar ser jag inga utdata.

A. Vanligtvis inte ser några multipathed vägar tyder på ett problem med multipathing demon, och det är `multipath.conf` mest troligt att alla problem här ligger i filen.

Det skulle också vara värt att kontrollera att du faktiskt kan se några diskar efter anslutning till målet, eftersom inget svar från multipath listor kan också innebära att du inte har några diskar.

* Använd följande kommando för att söka igenom SCSI-bussen igen:
  
    `$ rescan-scsi-bus.sh`(del av sg3_utils paket)
* Skriv följande kommandon:
  
    `$ dmesg | grep sd*`
     
     Eller
  
    `$ fdisk -l`
  
    Dessa returnerar information om nyligen tillagda diskar.
* Om du vill ta reda på om det är en StorSimple-disk använder du följande kommandon:
  
    `cat /sys/block/<DISK>/device/model`
  
    Detta returnerar en sträng som avgör om det är en StorSimple-disk.

En mindre sannolik men möjlig orsak kan också vara inaktuella iscsid pid. Använd följande kommando för att logga ut från iSCSI-sessionerna:

    iscsiadm -m node --logout -p <Target_IP>

Upprepa det här kommandot för alla anslutna nätverksgränssnitt på iSCSI-målet, som är din StorSimple-enhet. När du har loggat ut från alla iSCSI-sessioner använder du iSCSI-mål-IQN för att återupprätta iSCSI-sessionen. Ange följande kommando:

    iscsiadm -m node --login -T <TARGET_IQN>


F. Jag är inte säker på om min enhet är vitlistad.

A. Om du vill kontrollera om enheten är vitlistad använder du följande interaktiva felsökningskommando:

    multipathd -k
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


Mer information finns i [felsökning för multipathing](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Lista över användbara kommandon
| Typ | Kommando | Beskrivning |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Starta iSCSI-tjänsten |
| &nbsp; |`service iscsid stop` |Stoppa iSCSI-tjänsten |
| &nbsp; |`service iscsid restart` |Starta om iSCSI-tjänsten |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Upptäck tillgängliga mål på den angivna adressen |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Logga in på iSCSI-målet |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Logga ut från iSCSI-målet |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Skriv ut iSCSI-initierarnamn |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Kontrollera tillståndet för iSCSI-sessionen och volymen som upptäcktes på värden |
| &nbsp; |`iscsi -m session` |Visar alla iSCSI-sessioner som upprättats mellan värden och StorSimple-enheten |
|  | | |
| **Multipathing** |`service multipathd start` |Starta multipath daemon |
| &nbsp; |`service multipathd stop` |Stoppa multipath demon |
| &nbsp; |`service multipathd restart` |Starta om multipath demon |
| &nbsp; |`chkconfig multipathd on` </br> ELLER </br> `mpathconf -with_chkconfig y` |Aktivera multipath daemon för att starta vid uppstart |
| &nbsp; |`multipathd -k` |Starta den interaktiva konsolen för felsökning |
| &nbsp; |`multipath -l` |Lista multipath-anslutningar och enheter |
| &nbsp; |`mpathconf --enable` |Skapa en exempelmulitpath.conf-fil i`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Nästa steg
När du konfigurerar MPIO på Linux-värd kan du också behöva referera till följande CentoS 6.6-dokument:

* [Ställa in MPIO på CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux-utbildningsguide](http://linux-training.be/linuxsys.pdf)

