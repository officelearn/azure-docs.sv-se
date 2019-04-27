---
title: Installera Microsoft Azure StorSimple 8100-enhet | Microsoft Docs
description: Beskriver hur du packa upp, rackmontera och kabelansluta en StorSimple 8100-enhet innan du distribuerar och konfigurerar programvaran.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: b367b6e7126a442dc68646ff52a29c955f50b798
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631234"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Packa upp, rackmontera, och kabelansluta en StorSimple 8100-enhet
## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8100 är en enda hölje, rackmonterad enhet. Den här självstudien beskrivs hur du packar upp, rackmontera och kabel StorSimple 8100-enhet maskinvara innan du konfigurerar och distribuera StorSimple-enhet.

## <a name="unpack-your-storsimple-8100-device"></a>Packa upp din StorSimple 8100-enhet
Följande steg ger tydliga, detaljerade instruktioner om hur du packar upp StorSimple 8100 lagringsenheten. Den här enheten levereras i en enda låda.

### <a name="prepare-to-unpack-your-device"></a>Förbereda för att packa upp din enhet
Granska följande information innan du packar upp enheten.

![Varningsikon](./media/storsimple-safety/IC740879.png)![kraftiga ikonen](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

1. Se till att du har två personer som är tillgängliga för att hantera vikten för höljet om du hanterar det manuellt. Ett fullständigt konfigurerad hölje kan väga upp till 32 kg (70 lbs.).
2. Placera lådan på en plan, jämn yta.

Därefter gör du följande för att packa upp din enhet.

#### <a name="to-unpack-your-device"></a>Att packa upp din enhet
1. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna lådan. . [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att hjälpa dig att bedöma om enheten är i gott skick.
2. Packa upp lådan. Följande bild visar den uppackade vy över din StorSimple-enhet.
   
     ![Packa upp lagringsenheten](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Packa upp vy över lagringsenheten**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Paketering av statistik box |
   |   2 |Nedre skum |
   |   3 |Enhet |
   |   4 |Främsta skum |
   |   5 |Tillbehör box |
3. När du har packat upp lådan kontrollerar du att det finns:
   
   * 1 enhet för enkel hölje
   * 2 strömkablar
   * 1 övergång Ethernet-kabel
   * 2 Seriell konsol-kablar
   * 1 seriell USB-konverterare för seriell åtkomst
   * 1 manipuleringssäker T10 för
   * 4 QSFP-till-SFP +-kort för användning med 10 GbE-nätverksgränssnitt
   * 1-rackmonterade kit (2 sida rails med montera maskinvara)
   * Komma igång-dokumentationen
     
     Om du inte tog emot något av objekten som visas ovan, [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Nästa steg är att rackmontera enheten.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rackmonterade StorSimple 8100-enhet
Följ stegen nedan för att installera StorSimple 8100 lagringsenheten i ett standard 19-tums rack med främre och bakre inlägg. StorSimple 8100-enhet har en enda primär enhet.

Installationen består av flera steg, som beskrivs i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rackmonterade för att fungera korrekt.
> 
> 

### <a name="prepare-the-site"></a>Förbereda platsen
Enheten måste installeras i en standard 19-tums rack som har både främre och bakre inlägg. Använd följande procedur för att förbereda för Rackinstallation av.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Förbereda platsen för Rackinstallation
1. Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta (eller liknande).
2. Kontrollera att den plats där du vill konfigurera har standard nätström från en oberoende källa eller en rack power distributionsenhet (PDU) med en avbrottsfri elkälla (UPS).
3. Se till att en 2U-platsen är tillgänglig på rack där du vill montera enheten.

![Varningsikon](./media/storsimple-safety/IC740879.png)![kraftiga ikonen](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

Se till att du har två personer som är tillgängliga för att hantera vikten om du hanterar installationen av enheten manuellt. Ett fullständigt konfigurerad hölje kan väga upp till 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Rack krav
8100-hölje är utformat för installation i en standard 19-tums rack CAB med:

* Minsta djup 27.84 tum från rack efter att inlägget.
* Högsta vikt för 32 kg för enheten
* Maximal tryck av 5 Pascal (0,5 mätare).

### <a name="rack-mounting-rail-kit"></a>Rack montering rail kit
En uppsättning montera rails tillhandahålls för användning med 19-tums rack kabinettfilen. Rails har testats för att hantera maximala hölje vikt. Dessa rails kommer också att tillåta installation av flera höljen utan att förlora utrymme i racket.

#### <a name="to-install-the-device-on-the-rails"></a>Installera enheten på rails
1. Utför det här steget endast om inre rails inte är installerade på din enhet. De inre rails installeras vanligtvis på fabriken. Om rails inte installeras, installerar du vänster-spår och höger rail presentationen till sidor av hölje chassi. De bifoga sex mått skruvar på varje sida. För att hjälpa till med orientering, rail presentationen markeras **LH – Front** och **RH – Front**, och i slutet som fästs bakåt från höljet har en avsmalnande slutet.<br/>
   
    ![Bifoga rail bilder till hölje chassi](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Bifoga inre rail bilder till sidor av höljet**
   
    Label (Etikett) | Beskrivning
    ----- | -----------
    1     | M 3 x 4 knappen head skruvar
    2     | Chassi-bilder

2. Koppla den yttre vänstra spår och yttre högra rail sammansättningar till rack CAB lodrät medlemmar. Hakparenteserna markeras **LH**, **RH**, och **den här sidan uppåt** att guida dig genom rätt orientering.
3. Leta upp spårstiften på framsidan och baksidan av spårenheten. Utöka spår för att passa mellan rack inlägg och infoga de PIN-koderna i främre och bakre rack efter vertikal medlem hål. Var noga med att rail-sammansättningen är nivå.
4. Använd två av de angivna mått skruvarna för att skydda rail sammansättningen till racket lodrät medlemmar. Använd en skruv på klientwebbservern och en på Bakåt.
5. Upprepa dessa steg för den andra rail-sammansättningen.<br/>
   
     ![Bifoga rail bilder till rack CAB](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Koppla yttre rail sammansättningar till racket**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Ihopfogning skruv |
   |   2 |Kvadrat hål front rack efter skruv |
   |   3 |Vänstra rail front plats PIN-koder |
   |   4 |Ihopfogning skruv |
   |   5 |Vänstra rail bakre plats PIN-koder |

### <a name="mounting-the-device-in-the-rack"></a>Montera enheten i racket
Med de rack rails som precis har installerats kan utföra följande steg om du vill montera enheten i racket.

#### <a name="to-mount-the-device"></a>Montera enheten
1. Med en assistent lyfta höljet och justera den med rack rails.
2. Infoga noggrant enheten i rails, och skickar sedan enheten helt i racket kabinettfil.<br/>
   
    ![Infoga enheten i racket](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montera enheten i racket**
3. Ta bort vänster och höger främre flänsad caps genom att hämta caps kostnadsfritt. Flänsad caps Fäst helt enkelt till flänsar.
4. Skydda höljet racken genom att installera en angiven Phillips head skruv via varje flänsad vänster och höger.
5. Installera flänsad caps genom att trycka på dem på plats och fästa dem på plats.<br/>
   
     ![Installera flänsad caps](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera flänsad caps**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Hölje flygplanet skruv |

Nästa steg är att kabelanslut din enhet till ström, nätverk och serieåtkomst.

## <a name="cable-your-storsimple-8100-device"></a>Kabelansluta en StorSimple 8100-enhet
Följande procedurer beskriver hur du kabelansluta en StorSimple 8100-enhet till ström, nätverk och seriella anslutningar.

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar kabelansluta enheten behöver du:

* Dina lagringsenhet, helt Uppackad och rack monterade.
* 2 strömkablar som medföljde din enhet
* Åtkomst till 2 Kraftfördelningsenheter (rekommenderas).
* Nätverkskablar
* Angivna seriella kablar
* Seriell USB converter med lämplig drivrutin installeras på din dator (vid behov)
* Tillhandahålls 4 QSFP-till-SFP +-kort för användning med 10 GbE-nätverksgränssnitt
* [Maskinvara som stöds för 10 GbE-nätverksgränssnitt på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Kablar
Din enhet innehåller redundant ström och kylning moduler (PCMs). Både PCMs måste vara installerat och anslutet till olika strömkällor att säkerställa hög tillgänglighet.

Utför följande steg för att kabelansluta den kraft.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Nätverkskablar
Enheten är en aktiv-standby-konfiguration: vid en given tidpunkt, en domänkontrollant-modul är aktiv och bearbetning av alla åtgärder på disk och nätverk när modulen andra domänkontrollanter är i vänteläge. Om en domänkontrollant misslyckas kontrollenheten i vänteläge aktiveras omedelbart och fortsätter disk- och nätverksfunktioner.

Du måste kabelanslut enheten nätverket enligt beskrivningen i följande steg för att stödja den här redundant kontrollenhetsredundans.

#### <a name="to-cable-for-network-connection"></a>Att kabel för nätverksanslutning
1. Enheten har sex nätverksgränssnitt på varje domänkontrollant: fyra 1 Gbit/s och två 10 Gbit/s Ethernet-portar. Identifiera de olika portarna för data på serverdelen för din enhet.
   
    ![Serverdelen av 8100-enhet](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tillbaka på enheten som visar dataportar**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   0,1,4,5 |1 GbE-nätverksgränssnitt |
   |   2,3 |10 GbE-nätverksgränssnitt |
   |   6 |Seriella portar |
2. Se i följande diagram nätverkskablar. (Lägsta nätverkskonfigurationen visas som heldragna blå linjer. Ytterligare konfiguration krävs för hög tillgänglighet och prestanda visas som prickade linjer.)

    ![Kabelansluta den 2U för nätverk](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Nätverket kablar för din enhet**

   |Label (Etikett) | Beskrivning |
   |----- | ----------- |
   | A    | LAN med Internetåtkomst |
   | B    | Kontrollenhet 0 |
   | C    | PCM 0 |
   | D    | Kontrollenhet 1 |
   | E    | PCM 1 |
   | F, G | Värdar |
   | 0-5  | Nätverksgränssnitt |



När kablar enheten kräver den lägsta konfigurationen:

* Minst två nätverksgränssnitt är anslutna på varje domänkontrollant för att komma åt cloud och en för iSCSI. DATA 0 port aktiveras och konfigureras via seriekonsolen för enheten automatiskt. Förutom DATA 0 måste en annan dataporten också konfigureras via den klassiska Azure-portalen. I det här fallet ansluta DATA 0 porten till den primära LAN (nätverk med åtkomst till Internet). Andra dataportar kan anslutas till SAN/iSCSI LAN (VLAN)-segmentet i nätverket, beroende på den avsedda rollen.
* Samma gränssnitt på varje domänkontrollant är anslutna till samma nätverk för att säkerställa tillgänglighet om det uppstår redundans controller. Exempelvis om du vill ansluta DATA 0 och DATA 3 för en av kontrollenheterna måste du ansluta motsvarande DATA 0 och DATA 3 på den andra styrenheten.

Ha i åtanke för hög tillgänglighet och prestanda:

* Konfigurera ett par med nätverksgränssnitt för molnåtkomst (1 GbE) och en annan par för iSCSI (10 GbE rekommenderas) på varje domänkontrollant när det är möjligt.
* Om det är möjligt ska du ansluta nätverksgränssnitt från varje styrenhet till två olika växlar för att säkerställa tillgänglighet mot ett switch-fel. Bilden visar två 10 GbE nätverksgränssnitt, DATA 2 och DATA 3 från varje domänkontrollant som är ansluten till två olika växlar.

Mer information finns i den **nätverksgränssnitt** under den [krav på hög tillgänglighet för StorSimple-enheten](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om du använder SFP + mottagarna med din 10 GbE-nätverksgränssnitt kan använda den angivna QSFP-SFP +-kort. Mer information går du till [maskinvara som stöds för 10 GbE-nätverksgränssnitt på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Seriell port kablar
Utför följande steg för att kabelanslut din seriell port.

#### <a name="to-cable-for-serial-connection"></a>Att kabel för seriell anslutning
1. Enheten har en seriell port på varje domänkontrollant som identifieras med en skiftnyckelikonen. Se bilden i den [nätverkskablar](#network-cabling) avsnitt för att hitta de seriella portarna på serverdelen för din enhet.
2. Identifiera den aktiva kontrollenheten på din enhet-bakplan. En blinkande blå LED indikerar att kontrollanten är aktiv.
3. Använd de angivna seriella kablarna (vid behov, USB-seriell konverterare för din bärbara dator) och Anslut konsolen eller dator (med terminalemulering till enheten) till den seriella porten på den aktiva kontrollenheten.
4. Installera seriell USB-drivrutiner (medföljer enheten) på datorn.
5. Konfigurera seriell anslutning enligt följande: styra inställd på Ingen 115 200 bit/s, 8 databitar, 1 stop-bitars, ingen paritet och flow.
6. Kontrollera att anslutningen fungerar genom att trycka på RETUR på konsolen. Menyn för seriekonsolen bör visas.

> [!NOTE]
> **Lights-Out-hantering**: När enheten installeras i ett fjärranslutet datacenter eller i ett rum med begränsad åtkomst som gäller datorn, måste du kontrollera att de seriella anslutningarna till båda styrenheterna alltid är anslutna till en seriell konsol växel eller liknande utrustning. Detta gör out-of-band-fjärrstyrning och stöd för åtgärder om det finns nätverksstörningar eller oväntade fel.
> 
> 

Enheten är nu kabelansluten till ström, nätverksåtkomst och seriell anslutning. Nästa steg är att konfigurera programvaran och distribuera din enhet.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuera och konfigurera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

