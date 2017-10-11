---
title: "Maskinvarukomponenter för StorSimple 8000-serien och status | Microsoft Docs"
description: "Lär dig hur du övervakar maskinvarukomponenter av StorSimple-enheten via tjänsten StorSimple Enhetshanteraren."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 90724099842eac513c39dccf113ad1c0a63983f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Använda tjänsten StorSimple Device Manager för att övervaka maskinvarukomponenter och status
## <a name="overview"></a>Översikt
Den här artikeln beskriver de olika fysiska och logiska komponenterna i din lokala StorSimple 8000-serieenhet. Beskriver även hur du övervakar Komponentstatus enhet med hjälp av den **Status och maskinvara hälsa** bladet i StorSimple enheten Manager-tjänsten.

Den **Status och maskinvara hälsa** bladet visar maskinvarustatus för alla komponenter för StorSimple-enhet.

Det finns tre avsnitt som beskriver listan med komponenter för 8100:

* **Delade komponenter** – dessa inte är del av domänkontrollanterna som diskenheter, höljet, PCM komponenter och PCM temperatur rad spänning och rad aktuella sensorer.
* **Komponenter för styrenhet 0** – de komponenter som finns på en domänkontrollant 0, som domänkontrollant, SAS expander och koppling, temperatursensorer domänkontrollant och olika nätverksgränssnitt.
* **Kontrollant 1 komponenter** – de komponenter som utgör Controller 1, liknande de detaljerade för styrenhet 0.

En 8600-enhet har ytterligare komponenter som motsvarar höljet utökad Bunch av diskar (EBOD). Det finns fem avsnitt under listan med komponenter. Det finns tre avsnitt som innehåller komponenterna i primära höljet och är identiska med de som beskrivs för 8100 av dessa. Det finns två ytterligare avsnitt för höljet EBOD som beskriver:

* **EBOD styrenhet 0 komponenter** – de komponenter som finns på EBOD hölje 0, till exempel EBOD-styrenhet, SAS-expander och koppling och domänkontrollant temperatursensorer.
* **EBOD domänkontrollant 1 komponenter** – de komponenter som utgör EBOD hölje 1, liknar de detaljerade för EBOD hölje 0.
* **EBOD hölje delade komponenter** – komponenterna finns i EBOD höljet och PCM som inte är en del av EBOD-styrenhet.

> [!NOTE]
> **Maskinvarustatus är inte tillgänglig för en för StorSimple molnet (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Övervaka maskinvarustatus
Utför följande steg om du vill visa maskinvarustatus för en enhetskomponent:

1. Gå till **enheter**, Välj en specifik StorSimple-enhet. Gå till **övervakaren > maskinvara hälsa**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Leta upp den **maskinvarukomponenter** avsnittet och välja bland tillgängliga komponenter. Klicka bara på etiketten expandera listan och visa status för de olika komponenterna i enheten. Finns det [detaljerad komponentlista för primära höljet](#component-list-for-primary-enclosure-of-storsimple-device) och [detaljerad komponentlista för höljet EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Använd följande färg kodning för att tolka Komponentstatus:
   
   * **Grön Kontrollera** – anger en felfri komponent med **OK** status.
   * **Gult** – anger en försämrad komponent i **varning** tillstånd.
   * **Rött utropstecken** – Denotes en felande komponent som har en **fel** status.
   * **Vit med svart text** – anger en komponent som inte finns.
   
   Följande skärmbild visar en enhet som har komponenter i **OK**, **varning**, och **fel** tillstånd.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandera den **delade komponentlistan**, kan vi se att NVRAM och klustret är försämrade.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Expandera den **domänkontrollant 1 komponenter** lista, kan vi se att klusternoden har misslyckats.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Om du stöter på en komponent som inte är i en **felfri** tillstånd, kontakta Microsoft Support. Om aviseringar ska aktiveras på enheten, får du en e-postavisering. Om du behöver ersätta en misslyckad maskinvarukomponent finns [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista över komponenten för primära höljet av StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenter som ingår i primära höljet (finns både i 8100 och 8600) på din lokala StorSimple-enhet.

| Komponent | Modul | Typ | Plats | Fältet utbytbara enhet (FRU)? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Enheten på platsen [0-11] |Diskenheter |Fysiska |Delad |Ja |En rad visas för varje SSD och HDD-enheter i det primära höljet. |
| Omgivande-temperatursensor |Höljet |Fysiska |Delad |Nej |Mäter temperatur inom chassit. |
| Halva plan-temperatursensor |Höljet |Fysiska |Delad |Nej |Mäter temperatur halva plan. |
| Akustiskt larm |Höljet |Fysiska |Delad |Nej |Anger om undersystemet akustiskt larm inom chassit fungerar. |
| Höljet |Höljet |Fysiska |Delad |Ja |Anger förekomsten av ett chassi. |
| Inställningar för höljet |Höljet |Fysiska |Delad |Nej |Refererar till chassit frontpanel. |
| Raden spänningssensorer |PCM |Fysiska |Delad |Nej |Flera rad spänningssensorer har det tillståndet som visas som anger om uppmätta spänningen ligger inom Toleransvärdena. |
| Raden aktuella sensorer |PCM |Fysiska |Delad |Nej |Ett stort antal rad aktuella sensorer ha deras status visas som anger om aktuellt med uppmätt ligger inom Toleransvärdena. |
| Temperatursensorer i PCM |PCM |Fysiska |Delad |Nej |Ett stort antal Hotspot sensorer och temperatursensorer som Inlet har sin status visas som anger om uppmätta temperaturen ligger inom Toleransvärdena. |
| Strömförsörjning [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för varje strömkällor i två PCMs finns på baksidan av enheten. |
| Kylning [0-1] |PCM |Fysiska |Delad |Ja |En rad som presenteras för var och en av de fyra kylfläktar som finns i två PCMs. |
| Batteri [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för varje säkerhetskopiering batteri moduler som har satts i PCM. |
| Metis |Saknas |Logiska |Delad |Saknas |Visar tillståndet för batterierna: om de behöver debitering och närmar sig slutet av livslängd. |
| Kluster |Saknas |Logiska |Delad |Saknas |Visar tillståndet för det kluster som skapas mellan två integrerad styrenhet moduler. |
| Klusternod |Saknas |Logiska |Delad |Saknas |Visar tillståndet för domänkontrollanten som en del av klustret. |
| Klusterkvorum |Saknas |Logiska | |Saknas |Indikerar förekomst av majoritet disk medlemskap i lagringspoolen Hårddisk. |
| Hårddisk datautrymme |Saknas |Logiska |Delad |Saknas |Det lagringsutrymme som används för data i lagringspoolen hårddisken (HDD). |
| Hårddisk hantering av utrymme |Saknas |Logiska |Delad |Saknas |Det här utrymmet i lagringspoolen Hårddisk för hanteringsuppgifter. |
| Hårddisk kvorum utrymme |Saknas |Logiska |Delad |Saknas |Det här utrymmet i lagringspoolen Hårddisk för klustrets kvorum. |
| Hårddisk ersättning utrymme |Saknas |Logiska |Delad |Saknas |Det här utrymmet i lagringspoolen Hårddisk för ersättning av domänkontrollant. |
| SSD datautrymme |Saknas |Logiska |Delad |Saknas |Lagringsutrymme som används för data i lagringspoolen Solid-State-hårddisk (SSD). |
| SSD NVRAM utrymme |Saknas |Logiska |Delad |Saknas |Lagringsutrymmet i SSD lagringspoolen som är dedikerad för NVRAM logik. |
| HDD-lagringspoolen |Saknas |Logiska |Delad |Saknas |Visar tillståndet för logiska lagringspoolen som skapas från enhet hårddiskar. |
| SSD-lagringspoolen |Saknas |Logiska |Delad |Saknas |Visar tillståndet för den logiska lagringspoolen som skapas från SSD-enhet. |
| Domänkontrollant [0-1] [tillstånd] |I/O |Fysiska |Domänkontrollant |Ja |Visar status för styrenheten, och om den är aktiv eller i vänteläge läge i chassit. |
| Temperatursensorer i controller |I/O |Fysiska |Domänkontrollant |Nej |Ett stort antal temperatursensorer som i/o-modul, CPU-temperatur, DIMM och PCIe sensorer har det tillståndet som visas som anger huruvida temperaturen påträffade ligger inom Toleransvärdena. |
| SAS expander |I/O |Fysiska |Domänkontrollant |Nej |Visar tillståndet för den seriellt anslutna SCSI (SAS) expander, som används för att ansluta integrerad lagring till styrenheten. |
| SAS-koppling [0-1] |I/O |Fysiska |Domänkontrollant |Nej |Visar status för varje SAS-koppling som används för att ansluta integrerad lagring till SAS-expander. |
| Flesta SBB halva plan sammankoppling |I/O |Fysiska |Domänkontrollant |Nej |Visar status för anslutningen halva plan som används för att ansluta varje styrenhet halva plan. |
| Processorkärna |I/O |Fysiska |Domänkontrollant |Nej |Visar status för processorkärnor inom varje styrenhet. |
| Höljet electronics power |I/O |Fysiska |Domänkontrollant |Nej |Anger används av höljet power-systemets tillstånd. |
| Höljet electronics diagnostik |I/O |Fysiska |Domänkontrollant |Nej |Visar status för diagnostik-undersystem som tillhandahålls av kontrollanten. |
| Hanteringsstyrenhet för baskort (BMC) |I/O |Fysiska |Domänkontrollant |Nej |Visar tillståndet för den hanteringsstyrenhet för baskort (BMC), vilket är en specialiserad tjänst processor som övervakar maskinvaruenheter via sensorer och kommunicerar med systemadministratören via en oberoende anslutning. |
| Ethernet |I/O |Fysiska |Domänkontrollant |Nej |Visar status för varje nätverksgränssnitt, det vill säga hantering och dataportar som anges på domänkontrollanten. |
| NVRAM |I/O |Fysiska |Domänkontrollant |Nej |Visar status för NVRAM, ett beständigt minne säkerhetskopierats av batteri som används för att behålla program-kritisk information vid strömavbrott. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista över komponenten för EBOD hölje av StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenter som ingår i EBOD höljet (endast tillgängligt i 8600-modellen) i din lokala StorSimple-enhet.

| Komponent | Modul | Typ | Plats | FRU? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Enheten på platsen [0-11] |Diskenheter |Fysiska |Delad |Ja |En rad visas för varje HDD-enheterna först i EBOD höljet. |
| Omgivande-temperatursensor |Höljet |Fysiska |Delad |Nej |Mäter temperatur inom chassit. |
| Halva plan-temperatursensor |Höljet |Fysiska |Delad |Nej |Mäter temperatur halva plan. |
| Akustiskt larm |Höljet |Fysiska |Delad |Nej |Anger om undersystemet akustiskt larm inom chassit fungerar. |
| Höljet |Höljet |Fysiska |Delad |Ja |Anger förekomsten av ett chassi. |
| Inställningar för höljet |Höljet |Fysiska |Delad |Nej |Refererar till OPS eller chassit frontpanel. |
| Raden spänningssensorer |PCM |Fysiska |Delad |Nej |Flera rad spänningssensorer har det tillståndet som visas som anger om uppmätta spänningen ligger inom Toleransvärdena. |
| Raden aktuella sensorer |PCM |Fysiska |Delad |Nej |Ett stort antal rad aktuella sensorer ha deras status visas som anger om aktuellt med uppmätt ligger inom Toleransvärdena. |
| Temperatursensorer i PCM |PCM |Fysiska |Delad |Nej |Ett stort antal Hotspot sensorer och temperatursensorer som Inlet ha deras status visas som anger om uppmätta temperaturen ligger inom Toleransvärdena. |
| Strömförsörjning [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för varje strömkällor i två PCMs finns på baksidan av enheten. |
| Kylning [0-1] |PCM |Fysiska |Delad |Ja |En rad som presenteras för var och en av de fyra kylfläktar som finns i två PCMs. |
| Lokal lagring [HDD] |Saknas |Logiska |Delad |Saknas |Visar tillståndet för logiska lagringspoolen som skapas från enhet hårddiskar. |
| Domänkontrollant [0-1] [tillstånd] |I/O |Fysiska |Domänkontrollant |Ja |Visar tillståndet för domänkontrollanterna i modulen EBOD. |
| Temperatursensorer i EBOD |I/O |Fysiska |Domänkontrollant |Nej |Ett stort antal temperatursensorer från varje domänkontrollant har det tillståndet som visas som anger om temperaturen påträffade ligger inom Toleransvärdena. |
| SAS expander |I/O |Fysiska |Domänkontrollant |Nej |Visar status för SAS expander, som används för att ansluta integrerad lagring till styrenheten. |
| SAS-koppling [0-2] |I/O |Fysiska |Domänkontrollant |Nej |Visar status för varje SAS-koppling som används för att ansluta integrerad lagring till SAS-expander. |
| Flesta SBB halva plan sammankoppling |I/O |Fysiska |Domänkontrollant |Nej |Visar status för anslutningen halva plan som används för att ansluta varje styrenhet halva plan. |
| Höljet electronics power |I/O |Fysiska |Domänkontrollant |Nej |Anger används av höljet power-systemets tillstånd. |
| Höljet electronics diagnostik |I/O |Fysiska |Domänkontrollant |Nej |Visar status för diagnostik-undersystem som tillhandahålls av kontrollanten. |
| Anslutning till enhetens styrenhet |I/O |Fysiska |Domänkontrollant |Nej |Visar status för anslutningen mellan EBOD i/o-modulen och enhetskontroll. |

## <a name="next-steps"></a>Nästa steg
* Om du vill använda Enhetshanteraren för StorSimple-tjänsten för att administrera din enhet, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
* Om du behöver felsöka en enhetskomponent som har status degraderad eller inte fungerar, se [StorSimple övervakning indikatorer](storsimple-monitoring-indicators.md).
* Om du vill ersätta en misslyckad maskinvarukomponent finns [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).
* Om du fortfarande har problem med enheter, [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md).

