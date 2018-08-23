---
title: Maskinvarukomponenter i StorSimple 8000-serien och status | Microsoft Docs
description: Lär dig hur du övervakar maskinvarukomponenter i StorSimple-enheten via StorSimple Device Manager-tjänsten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 58007eea9ce25423bc3a9c2847de42db04be43eb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055416"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Använda StorSimple Device Manager-tjänsten för att övervaka de komponenter och status

## <a name="overview"></a>Översikt
Den här artikeln beskrivs de olika fysiska och logiska komponenterna i din lokala StorSimple 8000 series-enhet. Det beskriver också hur du övervakar Komponentstatus enheten med den **Status och hälsotillstånd för maskinvara** bladet i StorSimple Device Manager-tjänsten.

Den **Status och hälsotillstånd för maskinvara** bladet visar maskinvarustatus för alla komponenter för StorSimple-enheten.

Det finns tre avsnitt som beskriver i listan med komponenter för 8100:

* **Delade komponenter** – dessa inte är en del av domänkontrollanterna som diskenheter, hölje, ström och kylning modulen (PCM) komponenter och PCM temperatur, rad spänning och rad aktuella sensorer.
* **-Komponenter kontrollenhet 0** – de komponenter som finns på kontrollenhet 0, som domänkontrollant, SAS-expanderare och anslutningen, controller temperatursensorer och olika nätverksgränssnitt.
* **-Komponenter kontrollenhet 1** – komponenterna som utgör kontrollenhet 1, liknar de som beskrivs för kontrollenhet 0.

En 8600-enhet har ytterligare komponenter som motsvarar höljet utökade Bunch av diskar (EBOD). Under listan över komponenter finns det fem avsnitt. Det finns tre avsnitt som innehåller komponenterna i primära höljet och är identiska med de som beskrivs för 8100 av dessa. Det finns två ytterligare avsnitt för EBOD-hölje som beskriver:

* **EBOD-kontrollanten 0 komponenter** – de komponenter som finns på EBOD hölje 0, till exempel EBOD-kontrollanten SAS-expanderare och anslutningen och controller temperatursensorer.
* **EBOD-komponenter kontrollenhet 1** – komponenterna som utgör EBOD hölje 1, liknar de detaljerade för EBOD hölje 0.
* **EBOD hölje Shared Components** – komponenterna finns i EBOD-höljet och PCM som inte är en del av EBOD-kontrollanten.

> [!NOTE]
> **Maskinvarustatus är inte tillgänglig för en StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Övervaka maskinvarustatus för
Utför följande steg om du vill visa maskinvarustatus för en enhetskomponent:

1. Gå till **enheter**, välja en specifik StorSimple-enhet. Gå till **övervakaren > hälsotillstånd för maskinvara**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Leta upp den **maskinvarukomponenter** avsnittet och välj bland tillgängliga komponenter. Klicka bara på etiketten för att expandera listan och visa status för de olika enhetskomponenterna. Se den [detaljerad komponentlistan för primära höljet](#component-list-for-primary-enclosure-of-storsimple-device) och [detaljerad komponentlistan för EBOD-höljet](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Använd följande färgschema för kodning för att tolka Komponentstatus:
   
   * **Grön Kontrollera** – anger en felfri komponent med **OK** status.
   * **Gul** – anger en försämrad komponent i **varning** tillstånd.
   * **Rött utropstecken** – Denotes en misslyckad komponent som har en **fel** status.
   * **Vit med svart text** – anger att en komponent som inte finns.
   
   Följande skärmbild visar en enhet som har komponenter i **OK**, **varning**, och **fel** tillstånd.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandera den **delade komponenter lista**, kan vi se att NVRAM och klustret är försämrade.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Expandera den **komponenter kontrollenhet 1** lista, kan vi se att klusternoden har misslyckats.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Om det uppstår en komponent som inte finns i en **felfri** tillstånd, kontakta Microsoft Support. Om aviseringar ska aktiveras på din enhet får du en e-postavisering. Om du vill ersätta en misslyckad maskinvarukomponent kan se [StorSimple maskinvaruersättning komponenten](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Komponentlistan för primära inneslutning av StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenter som ingår i det primära höljet (finns både i 8100 och 8600) i din lokala StorSimple-enheten.

| Komponent | Modul | Typ | Plats | Fältet replaceable enhet (FRU)? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Enhet i fack [0-11] |Diskenheter |Fysiska |Delad |Ja |En rad visas för varje SSD eller HDD-enheter i det primära höljet. |
| Omgivningstemperatursensor |Hölje |Fysiska |Delad |Nej |Mäter temperaturen inom chassit. |
| Mitten plan-temperatursensor |Hölje |Fysiska |Delad |Nej |Mäter temperaturen för mitten plan. |
| Ljudligt alarm |Hölje |Fysiska |Delad |Nej |Anger om undersystemet ljudligt alarm inom chassit är funktionell. |
| Hölje |Hölje |Fysiska |Delad |Ja |Anger förekomsten av ett chassi. |
| Inställningar för hölje |Hölje |Fysiska |Delad |Nej |Refererar till chassit frontpanel. |
| Rad spänningssensorer |PCM |Fysiska |Delad |Nej |Ett stort antal rad spänningssensorer har deras status visas som anger om uppmätta spänningen ligger inom Toleransvärdena. |
| Rad aktuella sensorer |PCM |Fysiska |Delad |Nej |Ett stort antal rad aktuella sensorer har det tillståndet som visas, vilket indikerar om den uppmätta aktuellt ligger inom Toleransvärdena. |
| Temperatursensorer i PCM |PCM |Fysiska |Delad |Nej |Ett stort antal temperatursensorer, till exempel Inlet och Hotspot sensorer har enheternas status visas som anger om uppmätta temperaturen är inom Toleransvärdena. |
| Strömförsörjning [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för var och en av strömförsörjningar i två PCMs finns på baksidan av enheten. |
| Kylning [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för var och en av de fyra kylfläktar som finns i två PCMs. |
| Batteri [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för var och en av de extrabatteri moduler som har satts i PCM. |
| Metis |Gäller inte |Logiska |Delad |Gäller inte |Visar tillståndet för batterierna: om de behöver debitering och närmar sig slutet på sin livscykel. |
| Kluster |Gäller inte |Logiska |Delad |Gäller inte |Visar tillståndet för det kluster som skapas mellan de två modulerna som integrerad styrenhet. |
| Klusternod |Gäller inte |Logiska |Delad |Gäller inte |Visar status för domänkontrollanten som en del av klustret. |
| Klusterkvorum |Gäller inte |Logiska | |Gäller inte |Anger förekomsten av majoritet disk medlemskap i lagringspool för Hårddisk. |
| Datautrymme |Gäller inte |Logiska |Delad |Gäller inte |Lagringsutrymmet som används för data i lagringspoolen hårddisken (HDD). |
| Hanteringsutrymme |Gäller inte |Logiska |Delad |Gäller inte |Det här utrymmet i lagringspool för Hårddisk för hanteringsuppgifter. |
| Utrymme för Hårddiskkvorum |Gäller inte |Logiska |Delad |Gäller inte |Det här utrymmet i lagringspool för Hårddisk för klustrets kvorum. |
| Utrymme för Hårddiskersättning |Gäller inte |Logiska |Delad |Gäller inte |Det här utrymmet i lagringspool för Hårddisk för ersättning av domänkontrollanten. |
| Datautrymme för SSD |Gäller inte |Logiska |Delad |Gäller inte |Lagringsutrymmet som används för data i lagringspoolen har solid state-hårddisk (SSD). |
| Utrymme för SSD NVRAM |Gäller inte |Logiska |Delad |Gäller inte |Lagringsutrymmet i lagringspool för SSD NVRAM logic dedikerade. |
| Lagringspool för Hårddisk |Gäller inte |Logiska |Delad |Gäller inte |Visar tillståndet för logiska lagringspoolen som skapas från enheten hårddiskar. |
| Lagringspool för SSD |Gäller inte |Logiska |Delad |Gäller inte |Visar tillståndet för den logiska lagringspool som har skapats från SSD-enhet. |
| Kontrollenhet [0-1] [status] |I/O |Fysiska |Kontrollenhet |Ja |Visar tillståndet för styrenhet, och om den är i aktiv eller standby-läge i chassit. |
| Temperatursensorer i kontrollanten |I/O |Fysiska |Kontrollenhet |Nej |Ett stort antal temperatursensorer som i/o-modul, CPU-temperatur, DIMM och PCIe sensorer har det tillståndet som visas, vilket indikerar huruvida temperaturen påträffade ligger inom Toleransvärdena. |
| SAS-expanderare |I/O |Fysiska |Kontrollenhet |Nej |Visar status för den seriellt anslutna SCSI (SAS) expander, som används för att ansluta den integrera lagringen till kontrollanten. |
| SAS-anslutning [0-1] |I/O |Fysiska |Kontrollenhet |Nej |Visar status för varje SAS-anslutning som används för att ansluta integrerad lagring till SAS-expanderare. |
| Flesta SBB mitten plan sammankoppling |I/O |Fysiska |Kontrollenhet |Nej |Anger tillståndet i mitten plan anslutningstjänsten, som används för att ansluta varje styrenhet till mitten plan. |
| Processorkärna |I/O |Fysiska |Kontrollenhet |Nej |Visar status för processorkärnor inom varje styrenhet. |
| Strömförsörjningssystem |I/O |Fysiska |Kontrollenhet |Nej |Visar status för power-system som används av höljet. |
| Elektronisk diagnostik för hölje |I/O |Fysiska |Kontrollenhet |Nej |Visar status för diagnostik-undersystem som tillhandahålls av kontrollanten. |
| Hanteringsstyrenhet för baskort (BMC) |I/O |Fysiska |Kontrollenhet |Nej |Visar status för huvudkortshantering (BMC), vilket är en specialiserad tjänst processor som övervakar maskinvarans via sensorer och kommunicerar med systemadministratören via en oberoende anslutning. |
| Ethernet |I/O |Fysiska |Kontrollenhet |Nej |Visar status för var och en av nätverksgränssnitt, det vill säga hantering och dataportar i styrenheten. |
| NVRAM |I/O |Fysiska |Kontrollenhet |Nej |Visar status för NVRAM, ett beständigt minne backas upp av batteri som används för att behålla program – viktig information vid strömavbrott. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Komponentlistan för EBOD inneslutning av StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenter som ingår i EBOD höljet (endast finns i 8600-modellen) i din lokala StorSimple-enheten.

| Komponent | Modul | Typ | Plats | FRU? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Enhet i fack [0-11] |Diskenheter |Fysiska |Delad |Ja |En rad visas för varje HDD-enheter beskrivs EBOD-höljet. |
| Omgivningstemperatursensor |Hölje |Fysiska |Delad |Nej |Mäter temperaturen inom chassit. |
| Mitten plan-temperatursensor |Hölje |Fysiska |Delad |Nej |Mäter temperaturen för mitten plan. |
| Ljudligt alarm |Hölje |Fysiska |Delad |Nej |Anger om undersystemet ljudligt alarm inom chassit är funktionell. |
| Hölje |Hölje |Fysiska |Delad |Ja |Anger förekomsten av ett chassi. |
| Inställningar för hölje |Hölje |Fysiska |Delad |Nej |Refererar till OPS eller chassit frontpanel. |
| Rad spänningssensorer |PCM |Fysiska |Delad |Nej |Ett stort antal rad spänningssensorer har deras status visas som anger om uppmätta spänningen ligger inom Toleransvärdena. |
| Rad aktuella sensorer |PCM |Fysiska |Delad |Nej |Ett stort antal rad aktuella sensorer har det tillståndet som visas, vilket indikerar om den uppmätta aktuellt ligger inom Toleransvärdena. |
| Temperatursensorer i PCM |PCM |Fysiska |Delad |Nej |Ett stort antal temperatursensorer, till exempel Inlet och Hotspot sensorer har det tillståndet visas som anger om uppmätta temperaturen ligger inom Toleransvärdena. |
| Strömförsörjning [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för var och en av strömförsörjningar i två PCMs finns på baksidan av enheten. |
| Kylning [0-1] |PCM |Fysiska |Delad |Ja |En rad visas för var och en av de fyra kylfläktar som finns i två PCMs. |
| Lokal lagring [HDD] |Gäller inte |Logiska |Delad |Gäller inte |Visar tillståndet för logiska lagringspoolen som skapas från enheten hårddiskar. |
| Kontrollenhet [0-1] [status] |I/O |Fysiska |Kontrollenhet |Ja |Visar tillståndet för styrenheterna i EBOD-modulen. |
| Temperatursensorer i EBOD |I/O |Fysiska |Kontrollenhet |Nej |Ett stort antal temperatursensorer från varje styrenhet har deras status visas som anger om temperaturen påträffade ligger inom Toleransvärdena. |
| SAS-expanderare |I/O |Fysiska |Kontrollenhet |Nej |Visar status för SAS-expanderare som används för att ansluta den integrera lagringen till kontrollanten. |
| SAS-anslutning [0-2] |I/O |Fysiska |Kontrollenhet |Nej |Visar status för varje SAS-anslutning som används för att ansluta integrerad lagring till SAS-expanderare. |
| Flesta SBB mitten plan sammankoppling |I/O |Fysiska |Kontrollenhet |Nej |Anger tillståndet i mitten plan anslutningstjänsten, som används för att ansluta varje styrenhet till mitten plan. |
| Strömförsörjningssystem |I/O |Fysiska |Kontrollenhet |Nej |Visar status för power-system som används av höljet. |
| Elektronisk diagnostik för hölje |I/O |Fysiska |Kontrollenhet |Nej |Visar status för diagnostik-undersystem som tillhandahålls av kontrollanten. |
| Anslutning till enhetens styrenhet |I/O |Fysiska |Kontrollenhet |Nej |Visar status för anslutningen mellan EBOD-i/o-modulen och enhetens styrenhet. |

## <a name="next-steps"></a>Nästa steg
* Om du vill använda StorSimple Device Manager-tjänsten för att administrera din enhet, gå till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
* Om du behöver felsöka en enhetskomponent som har statusen degraderad eller inte fungerar, se [StorSimple övervakningsindikatorer](storsimple-monitoring-indicators.md).
* Om du vill ersätta en misslyckad maskinvarukomponent, se [StorSimple maskinvaruersättning komponenten](storsimple-hardware-component-replacement.md).
* Om du fortfarande har problem med enheter [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

