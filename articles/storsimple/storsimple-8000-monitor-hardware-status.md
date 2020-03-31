---
title: Maskinvarukomponenter och status i StorSimple 8000-serien | Microsoft-dokument
description: Läs om hur du övervakar maskinvarukomponenterna på Din StorSimple-enhet via StorSimple Device Manager-tjänsten.
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
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254747"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Använda Tjänsten StorSimple Device Manager för att övervaka maskinvarukomponenter och status

## <a name="overview"></a>Översikt
I den här artikeln beskrivs de olika fysiska och logiska komponenterna i den lokala StorSimple 8000-serien. Det förklarar också hur du övervakar enhetens komponentstatus med hjälp av **hälsobladet Status och maskinvara** i Tjänsten StorSimple Device Manager.

Hälsobladet **Status och maskinvara** visar maskinvarustatus för alla StorSimple-enhetskomponenter.

Under listan över komponenter för 8100 finns det tre avsnitt som beskriver:

* **Delade komponenter** – Dessa är inte en del av styrenheterna, till exempel diskenheter, kapsling, power och cooling module (PCM) komponenter och PCM-temperatur, linjespänning och linjeströmssensorer.
* **Controller 0 Components** – De komponenter som finns på Controller 0, till exempel styrenhet, SAS expander och anslutning, styrenhetstemperatursensorer och de olika nätverksgränssnitten.
* **Controller 1 Components** – De komponenter som utgör Controller 1, liknande de som beskrivs för Controller 0.

En 8600-enhet har ytterligare komponenter som motsvarar EBOD-höljet (Extended Bunch of Disks). Under listan över komponenter finns fem avsnitt. Av dessa finns det tre avsnitt som innehåller komponenterna i den primära inneslutningen och är identiska med de som beskrivs för 8100. Det finns ytterligare två avsnitt för EBOD-kapslingen som beskriver:

* **EBOD Controller 0 Components** – De komponenter som finns på EBOD-hölje 0, till exempel EBOD-styrenheten, SAS expander och anslutning samt temperaturgivare för styrenheten.
* **EBOD Controller 1 Components** – De komponenter som utgör EBOD-kapsling 1, liknande de som anges för EBOD-kapsling 0.
* **EBOD-kapsling delade komponenter** – De komponenter som finns i EBOD-höljet och PCM som inte ingår i EBOD-styrenheten.

> [!NOTE]
> **Maskinvarustatusen är inte tillgänglig för en StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Övervaka maskinvarustatus
Gör följande för att visa maskinvarustatus för en enhetskomponent:

1. Navigera till **Enheter**och välj en specifik StorSimple-enhet. Gå till **Övervaka > maskinvaruhälsa**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Leta reda på avsnittet **Maskinvarukomponenter** och välj bland de tillgängliga komponenterna. Klicka bara på komponentetiketten för att expandera listan och visa status för de olika enhetskomponenterna. Se den [detaljerade komponentlistan för den primära kapslingen](#component-list-for-primary-enclosure-of-storsimple-device) och den [detaljerade komponentlistan för EBOD-kapslingen](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Använd följande färgkodningsschema för att tolka komponentstatusen:
   
   * **Grön kontroll** – Betecknar en felfri komponent med **OK-status.**
   * **Gul** – Betecknar en skadad komponent i **varningsläge.**
   * **Rött utrop** – Betecknar en misslyckad komponent som har **statusen Fel.**
   * **Vitt med svart text** – Betecknar en komponent som inte finns.
   
   Följande skärmbild visar en enhet som har komponenter i tillstånd **OK**, **Varning**och **Fel.**
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandera listan **Delade komponenter**kan vi se att NVRAM och klustret bryts ned.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Utöka **controller 1-komponentlistan** kan vi se att klusternoden har misslyckats.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Om du stöter på en komponent som inte är i **felfritt** tillstånd kontaktar du Microsoft Support. Om aviseringar är aktiverade på enheten får du en e-postavisering. Om du behöver byta ut en misslyckad maskinvarukomponent läser du [byte av StorSimple-maskinvarukomponent](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Komponentlista för primär hölje för StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenterna i den primära höljet (finns både i 8100 och 8600) på din lokala StorSimple-enhet.

| Komponent | Modul | Typ | Location | Fält utbytbar enhet (FRU)? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Kör i kortplats [0-11] |Diskenheter |Fysiska |Delad |Ja |En rad presenteras för var och en av SSD- eller hårddiskarna i den primära höljet. |
| Temperaturgivare för omgivning |Inhägnad |Fysiska |Delad |Inga |Mäter temperaturen i chassit. |
| Temperaturgivare för mellanplan |Inhägnad |Fysiska |Delad |Inga |Mäter temperaturen på mitten av planet. |
| Akustiskt larm |Inhägnad |Fysiska |Delad |Inga |Anger om det hörbara larmdelsystemet i chassit fungerar. |
| Inhägnad |Inhägnad |Fysiska |Delad |Ja |Anger att det finns ett chassi. |
| Inställningar för hölje |Inhägnad |Fysiska |Delad |Inga |Hänvisar till chassits frontpanel. |
| Sensorer för linjespänning |PCM |Fysiska |Delad |Inga |Många linjespänningssensorer har sitt tillstånd visat, vilket indikerar om den uppmätta spänningen är inom tolerans. |
| Linjeströmsensorer |PCM |Fysiska |Delad |Inga |Många linjeströmsensorer har sitt tillstånd visat, vilket indikerar om den uppmätta strömmen ligger inom tolerans. |
| Temperaturgivare i PCM |PCM |Fysiska |Delad |Inga |Många temperatursensorer som Inlopps- och hotspot-sensorer har sitt tillstånd visat, vilket indikerar om den uppmätta temperaturen ligger inom toleransen. |
| Strömförsörjning [0-1] |PCM |Fysiska |Delad |Ja |En linje presenteras för var och en av nätaggregaten i de två PCM som finns på baksidan av enheten. |
| Kylning [0-1] |PCM |Fysiska |Delad |Ja |En linje presenteras för var och en av de fyra kylfläktarna som bor i de två PCM.One line is presented for each of the four cooling fans residing in the two PCMs. |
| Batteri [0-1] |PCM |Fysiska |Delad |Ja |En linje presenteras för var och en av de reservbatterimoduler som sitter i PCM. |
| Metis |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Visar batteriernas tillstånd: om de behöver laddas och närmar sig uttjänt. |
| Kluster |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Visar tillståndet för klustret som skapas mellan de två integrerade styrenhetsmodulerna. |
| Klusternod |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Anger styrenhetens tillstånd som en del av klustret. |
| Klusterkvorum |Ej tillämpligt |Logiska | |Ej tillämpligt |Anger förekomsten av majoritetsdiskmedlemskap i hårddisklagringspoolen. |
| Hårddiskdatautrymme |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Det lagringsutrymme som används för data i hårddiskens (HDD) lagringspool. |
| Hanteringsutrymme för hårddiskar |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Det utrymme som reserverats i hdd-lagringspoolen för hanteringsuppgifter. |
| HDD kvorum utrymme |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Det utrymme som reserverats i hdd-lagringspoolen för klusterkvorum. |
| Ersättningsutrymme för hårddiskar |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Det utrymme som reserverats i hårddisklagringspoolen för ersättning av styrenheten. |
| SSD-datautrymme |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Lagringsutrymmet som används för data i lagringspoolen för SSD (Solid State Drive). |
| SSD NVRAM-utrymme |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Lagringsutrymmet i SSD-lagringspoolen som är avsedd för NVRAM-logik. |
| Hdd-lagringspool |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Visar tillståndet för den logiska lagringspool som skapas från enhets-hårddiskar. |
| SSD-lagringspool |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Visar tillståndet för den logiska lagringspoolen som skapas från enhets-SSD:er. |
| Controller [0-1] [tillstånd] |I/o |Fysiska |Controller |Ja |Visar styrenhetens tillstånd och om den är i aktivt eller vänteläge i chassit. |
| Temperaturgivare i styrenheten |I/o |Fysiska |Controller |Inga |Många temperatursensorer som I/O-modul, CPU-temperatur, DIMM- och PCIe-sensorer har sitt tillstånd visat, vilket indikerar om temperaturen som uppstått ligger inom toleransen eller inte. |
| SAS expander |I/o |Fysiska |Controller |Inga |Anger tillståndet för den seriella anslutna SCSI-expandern (SAS), som används för att ansluta den integrerade lagringen till styrenheten. |
| SAS-kontakt [0-1] |I/o |Fysiska |Controller |Inga |Anger tillståndet för varje SAS-anslutning, som används för att ansluta integrerad lagring till SAS expander. |
| SBB mellanplan sammanlänkning |I/o |Fysiska |Controller |Inga |Anger tillståndet för mellanplanets kontakt, som används för att ansluta varje styrenhet till mitten av planet. |
| Processor kärna |I/o |Fysiska |Controller |Inga |Anger processorkärnornas tillstånd i varje styrenhet. |
| Kapselelektronik effekt |I/o |Fysiska |Controller |Inga |Anger tillståndet för det kraftsystem som används av höljet. |
| Diagnostik av kapselektronik |I/o |Fysiska |Controller |Inga |Anger tillståndet för de undersystem för diagnostik som tillhandahålls av styrenheten. |
| Styrenhet för huvudkortshantering (BMC) |I/o |Fysiska |Controller |Inga |Anger tillståndet för den baseboard management controller (BMC), som är en specialiserad tjänstprocessor som övervakar maskinvaruenheten genom sensorer och kommunicerar med systemadministratören via en oberoende anslutning. |
| Ethernet |I/o |Fysiska |Controller |Inga |Anger tillståndet för var och en av nätverksgränssnitten, det vill säga hanterings- och dataportarna som finns på styrenheten. |
| Nvram |I/o |Fysiska |Controller |Inga |Anger tillståndet för NVRAM, ett icke-flyktigt slumpmässigt åtkomstminne som backas upp av batteriet och som tjänar till att behålla programkritisk information i händelse av strömavbrott. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Komponentlista för EBOD-hölje för StorSimple-enhet
I följande tabell beskrivs de fysiska och logiska komponenterna i EBOD-höljet (finns endast i 8600-modellen) på din lokala StorSimple-enhet.

| Komponent | Modul | Typ | Location | FRU, VAD ÄR DET MED DIG? | Beskrivning |
| --- | --- | --- | --- | --- | --- |
| Kör i kortplats [0-11] |Diskenheter |Fysiska |Delad |Ja |En linje presenteras för var och en av hårddiskarna på framsidan av EBOD-höljet. |
| Temperaturgivare för omgivning |Inhägnad |Fysiska |Delad |Inga |Mäter temperaturen i chassit. |
| Temperaturgivare för mellanplan |Inhägnad |Fysiska |Delad |Inga |Mäter temperaturen på mitten av planet. |
| Akustiskt larm |Inhägnad |Fysiska |Delad |Inga |Anger om det hörbara larmdelsystemet i chassit fungerar. |
| Inhägnad |Inhägnad |Fysiska |Delad |Ja |Anger att det finns ett chassi. |
| Inställningar för hölje |Inhägnad |Fysiska |Delad |Inga |Hänvisar till OPS eller chassits frontpanel. |
| Sensorer för linjespänning |PCM |Fysiska |Delad |Inga |Många linjespänningssensorer har sitt tillstånd visat, vilket indikerar om den uppmätta spänningen är inom tolerans. |
| Linjeströmsensorer |PCM |Fysiska |Delad |Inga |Många linjeströmsensorer har sitt tillstånd visat, vilket indikerar om den uppmätta strömmen ligger inom tolerans. |
| Temperaturgivare i PCM |PCM |Fysiska |Delad |Inga |Många temperatursensorer som Inlopps- och hotspot-sensorer har sitt tillstånd visat, vilket indikerar om den uppmätta temperaturen är inom tolerans. |
| Strömförsörjning [0-1] |PCM |Fysiska |Delad |Ja |En linje presenteras för var och en av nätaggregaten i de två PCM som finns på baksidan av enheten. |
| Kylning [0-1] |PCM |Fysiska |Delad |Ja |En linje presenteras för var och en av de fyra kylfläktarna som bor i de två PCM.One line is presented for each of the four cooling fans residing in the two PCMs. |
| Lokal lagring [HÅRDDISK] |Ej tillämpligt |Logiska |Delad |Ej tillämpligt |Visar tillståndet för den logiska lagringspool som skapas från enhets-hårddiskar. |
| Controller [0-1] [tillstånd] |I/o |Fysiska |Controller |Ja |Visar styrenheternas tillstånd i EBOD-modulen. |
| Temperaturgivare i EBOD |I/o |Fysiska |Controller |Inga |Många temperatursensorer från varje regulator har sitt tillstånd visat, vilket indikerar om temperaturen som påträffas är inom tolerans. |
| SAS expander |I/o |Fysiska |Controller |Inga |Anger tillståndet för SAS expander, som används för att ansluta den integrerade lagringen till styrenheten. |
| SAS-kontakt [0-2] |I/o |Fysiska |Controller |Inga |Anger tillståndet för varje SAS-anslutning, som används för att ansluta integrerad lagring till SAS expander. |
| SBB mellanplan sammanlänkning |I/o |Fysiska |Controller |Inga |Anger tillståndet för mellanplanets kontakt, som används för att ansluta varje styrenhet till mitten av planet. |
| Kapselelektronik effekt |I/o |Fysiska |Controller |Inga |Anger tillståndet för det kraftsystem som används av höljet. |
| Diagnostik av kapselektronik |I/o |Fysiska |Controller |Inga |Anger tillståndet för de undersystem för diagnostik som tillhandahålls av styrenheten. |
| Anslutning till enhetsstyrenhet |I/o |Fysiska |Controller |Inga |Anger tillståndet för anslutningen mellan EBOD I/O-modulen och enhetsstyrenheten. |

## <a name="next-steps"></a>Nästa steg
* Om du vill använda Tjänsten StorSimple Device Manager för att administrera enheten går du till [StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
* Om du behöver felsöka en enhetskomponent som har status för försämrad eller misslyckad läser du [StorSimple-övervakningsindikatorer](storsimple-monitoring-indicators.md).
* Information om hur du ersätter en maskinvarukomponent som inte har kunnat hända finns i [Byte av StorSimple-maskinvarukomponent](storsimple-hardware-component-replacement.md).
* Om du fortfarande får problem med enheten [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).

