---
title: Byt ut en PCM på storsimaple 8000-serien | Microsoft-dokument
description: Förklarar hur du tar bort och byter ut Power and Cooling Module (PCM) på Din StorSimple-enhet
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60632507"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Ersätta en energi- och kylningsmodul på StorSimple-enheten
## <a name="overview"></a>Översikt
Power and Cooling Module (PCM) i din Microsoft Azure StorSimple-enhet består av en strömförsörjnings- och kylfläktar som styrs via de primära och EBOD-kapslingarna. Det finns bara en modell av PCM som är certifierad för varje kapsling. Den primära kapslingen är certifierad för en 764 W PCM och EBOD-höljet är certifierat för en 580 W PCM. Även om PCM:erna för den primära kapslingen och EBOD-höljet är olika, är ersättningsproceduren identisk.

I den här självstudien beskrivs hur du:

* Ta bort en PCM
* Installera en ersättnings-PCM

> [!IMPORTANT]
> Innan du tar bort och byter ut en PCM bör du läsa säkerhetsinformationen i [StorSimples maskinvarukomponentersättning](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Innan du byter ut en PCM
Tänk på följande viktiga problem innan du byter ut din PCM:

* Om strömförsörjningen till PCM går sönder lämnar du den felaktiga modulen installerad, men tar bort nätsladden. Fläkten kommer att fortsätta att ta emot ström från höljet och fortsätta att ge korrekt kylning. Om fläkten misslyckas måste PCM bytas ut omedelbart.
* Innan du tar bort PCM kopplar du bort strömmen från PCM genom att stänga av huvudbrytaren (i förekommande fall) eller genom att fysiskt ta bort nätsladden. Detta ger en varning till ditt system om att en avstängning är nära förestående.
* Se till att den andra PCM fungerar för fortsatt systemdrift innan du byter ut den felaktiga PCM. En felaktig PCM måste bytas ut av en fullt fungerande PCM så snart som möjligt.
* PCM-modulbyte tar bara några minuter att slutföra, men det måste slutföras inom 10 minuter efter att du tagit bort den misslyckade PCM för att förhindra överhettning.
* Observera att de 764 W PCM-moduler som levereras från fabriken inte innehåller batterimodulen för säkerhetskopiering. Du måste ta bort batteriet från din felaktiga PCM och sedan sätta in det i ersättningsmodulen innan du utför bytet. Mer information finns i hur du [tar bort och sätter i en batterimodul för säkerhetskopiering](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Ta bort en PCM
Följ dessa instruktioner när du är redo att ta bort en PCM (Power and Cooling Module) från din Microsoft Azure StorSimple-enhet.

> [!NOTE]
> Innan du tar bort DIN PCM kontrollerar du att du har rätt ersättning (764 W för den primära höljet eller 580 W för EBOD-höljet).

#### <a name="to-remove-a-pcm"></a>Så här tar du bort en PCM
1. Klicka på Inställningar > övervaka > maskinvaruhälsa i Den klassiska **Azure-portalen**. Kontrollera status för PCM-komponenter under **Delade komponenter** för att identifiera vilken PCM som har misslyckats:
   
   * Om en strömförsörjning i PCM 0 har misslyckats blir statusen **för strömförsörjningen i PCM 0** röd.
   * Om en strömförsörjning i PCM 1 har misslyckats, kommer statusen **för strömförsörjningen i PCM 1** att vara röd.
   * Om fläkten i PCM 1 har misslyckats, kommer statusen för antingen **Cooling 0 för PCM 0** eller **Cooling 1 för PCM 0** att vara röd.
2. Leta reda på den misslyckade PCM på baksidan av den primära inneslutningen. Om du kör en 8600-modell identifierar du den primära höljet genom att titta på systemenhetens identifieringsnummer som visas på frontpanelens LED-display. Standardenhets-ID som visas på den primära höljet är **00,** medan standardenhets-ID som visas på EBOD-höljet är **01**. I följande diagram och tabell förklaras lysdiodernas frontpanel.
   
    ![System-ID på ops-panelen på framsidan](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Bild 1** Enhetens frontpanel  
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Ljudavstängning |
   | 2 |Systemström |
   | 3 |Modul fel |
   | 4 |Logiskt fel |
   | 5 |Enhets-ID-skärm |
3. The monitoring indicator LEDs in the back of the primary enclosure can also be used to identify the faulty PCM. Se följande diagram och tabell för att förstå hur du använder lysdioderna för att hitta den felaktiga PCM. Om till exempel lysdioden som motsvarar **fläkten misslyckas** tänds har fläkten misslyckats. På samma sätt, om lysdioden som motsvarar **AC Fail** lyser, har strömförsörjningen misslyckats. 
   
    ![Bakplan av pcm-övervakningsindikator för enheten](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Bild 2** Baksidan av PCM med indikatorlysdioder
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Strömavbrott |
   | 2 |Fläktfel |
   | 3 |Batterifel |
   | 4 |PCM OK |
   | 5 |Dc strömavbrott |
   | 6 |Batteriet är hälsosamt |
4. Se följande diagram på baksidan av StorSimple-enheten för att hitta den misslyckade PCM-modulen. PCM 0 är till vänster och PCM 1 är till höger. Tabellen som följer förklarar modulerna.
   
     ![Bakplan av enhetens primära kapslingsmoduler](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Bild 3** Baksidan av enheten med plug-in-moduler 
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollant 0 |
   | 4 |Kontrollant 1 |
5. Stäng av den felaktiga PCM-datorn och koppla bort nätsladden. Du kan nu ta bort PCM.
6. Ta tag i spärren och sidan av PCM-handtaget mellan tummen och pekfingret och tryck ihop dem för att öppna handtaget.
   
    ![Öppna PCM-handtag](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Bild 4** Öppna PCM-handtaget
7. Ta tag i handtaget och ta bort PCM.
   
    ![Ta bort PCM för enhet](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Bild 5** Ta bort PCM

## <a name="install-a-replacement-pcm"></a>Installera en ersättnings-PCM
Följ dessa instruktioner för att installera en PCM i Din StorSimple-enhet. Kontrollera att du har satt i batterimodulen för säkerhetskopiering innan du installerar pcm-ersättningen (gäller endast 764 W PCMs). Mer information finns i hur du [tar bort och sätter i en batterimodul för säkerhetskopiering](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Så här installerar du en PCM
1. Kontrollera att du har rätt ersättnings-PCM för den här höljet. Den primära kapslingen behöver en 764 W PCM och EBOD-höljet behöver en 580 W PCM. Du bör inte försöka använda 580 W PCM i primärhöljet eller 764 W PCM i EBOD-höljet. Följande bild visar var den här informationen ska identifieras på etiketten som är anbringad på PCM.
   
    ![PCM-etikett för enhet](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Bild 6** PCM-etikett
2. Kontrollera om det finns skador på höljet, med särskild uppmärksamhet på kontakterna. 
   
   > [!NOTE]
   > **Installera inte modulen om några kontaktstift är böjda.**
   > 
   > 
3. Med PCM-handtaget i öppet läge skjuter du modulen in i höljet.
   
    ![Installera PCM för enhet](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Bild 7** Installera PCM
4. Stäng PCM-handtaget manuellt. Du bör höra ett klick när handtaget spärren engagerar.
   
   > [!NOTE]
   > För att säkerställa att kontaktstiften har aktiverats kan du försiktigt dra i handtaget utan att lossa spärren. Om PCM glider ut innebär det att spärren stängdes innan kontakterna aktiverades.
   
5. Anslut strömkablarna till strömkällan och till PCM.
6. Säkra dragavlastningsbalarna.
7. Slå på PCM.
8. Kontrollera att ersättningen lyckades: i Azure-portalen för tjänsten StorSimple Device Manager navigerar du till enheten och går sedan till **Inställningar > Övervaka > maskinvaruhälsa**. Under de **delade komponenterna**ska PCM:s status vara grön.
   
   > [!NOTE]
   > Det kan ta några minuter för ersättning PCM att helt initiera.

## <a name="next-steps"></a>Nästa steg
Läs mer om byte av [Maskinvarukomponent i StorSimple](storsimple-8000-hardware-component-replacement.md).

