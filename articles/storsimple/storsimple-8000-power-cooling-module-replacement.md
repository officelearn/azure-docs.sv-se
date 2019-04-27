---
title: 'Ersätt en PCM: en på din enhet i StorSimple 8000-serien | Microsoft Docs'
description: Beskriver hur du tar bort och ersätter den kraft och kylning modulen (PCM) på din StorSimple-enhet
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632507"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Ersätt en ström och kylning modulen på StorSimple-enheten
## <a name="overview"></a>Översikt
Ström och kylning modulen (PCM) i Microsoft Azure StorSimple-enheten består av en strömförsörjning och kylfläktar som styrs från den primära servern och EBOD-höljen. Det är bara en PCM som är certifierade för varje kabinett. Primär höljet är certifierad för en 764 W PCM och EBOD-hölje är certifierad för en 580 W PCM. Även om PCMs för primära höljet och EBOD-kabinett är olika, är ersättning proceduren identiska.

I den här självstudien beskrivs hur du:

* Ta bort en PCM
* Installera en ersättning PCM

> [!IMPORTANT]
> Innan du tar bort och ersätter en PCM granska säkerhetsinformation i [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Innan du ersätter en PCM
Tänk på följande viktiga problem innan du ersätter din PCM:

* Om det inte går att strömförsörjning i PCM, lämna felaktiga installerat modulen, men ta bort kontakten. Fläkten fortsätter att ta emot power från höljet och fortsätta att tillhandahålla rätt kylning. Om fläkten misslyckas måste PCM ersättas omedelbart.
* Innan du tar bort PCM att koppla från kraften från PCM genom att stänga av den huvudsakliga växeln (om sådan finns) eller genom att fysiskt tar bort kontakten. Detta ger en varning visas för systemet att stänga power är nära förestående.
* Kontrollera att den andra PCM fungerar korrekt för fortsatt systemfunktioner innan du ersätter felaktiga PCM. En felaktig PCM måste ersättas med ett fullt fungerande PCM så snart som möjligt.
* PCM-modulen ersättning tar bara några minuter att slutföra, men den måste slutföras inom 10 minuter för att ta bort den misslyckade PCM för att förhindra överhettning.
* Observera att de ersättning 764 W PCM moduler levereras från fabriken inte innehåller extrabatteri modulen. Du måste ta bort batteriet från din felaktiga PCM och infoga dem i modulen ersättning innan du utför ersättningen. Läs mer om hur du [ta bort och infoga en extrabatteri modul](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Ta bort en PCM
Följ dessa anvisningar när du är redo att ta bort en ström och kylning modulen (PCM) från Microsoft Azure StorSimple-enheten.

> [!NOTE]
> Innan du tar bort din PCM kan du kontrollera att du har rätt ersättning (764 V för primära höljet) eller 580 W för EBOD-höljet.

#### <a name="to-remove-a-pcm"></a>Ta bort en PCM
1. I den klassiska Azure-portalen klickar du på **Inställningar > övervaka > hälsotillstånd för maskinvara**. Kontrollera status för PCM-komponenter under **delade komponenter** att identifiera vilka PCM misslyckades:
   
   * Om en strömförsörjning i PCM 0 har misslyckats, status för **strömförsörjning i PCM 0** blir röd.
   * Om en strömförsörjning i PCM 1 har misslyckats, status för **strömförsörjning i PCM 1** blir röd.
   * Om fläkten i PCM 1 har misslyckats, status för antingen **kylning 0 för PCM 0** eller **kylning 1 för PCM 0** blir röd.
2. Leta upp den misslyckade PCM på baksidan av primära höljet. Om du använder en modell 8600 identifiera primära höljet genom att titta på System enhet ID-numret visas på panelen LED-skärm. Standard enhets-ID som visas på det primära höljet är **00**, medan standard enhets-ID som visas på EBOD-hölje är **01**. Följande diagram och tabell förklarar frontpanel för LED-skärmen.
   
    ![Systemid för OPS frontpanel](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Bild 1** fram panelen för enheten  
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Ljud av |
   | 2 |System power |
   | 3 |Modulen fel |
   | 4 |Logiska fel |
   | 5 |Visa för enhet-ID |
3. Övervakningsindikatorer på baksidan primära höljet kan också användas för att identifiera den felaktiga PCM. Se följande diagram och tabell för att förstå hur du använder de led: ar för att hitta den felaktiga PCM. Till exempel om det LED som motsvarar den **fläkt misslyckas** är tänd, fläkten misslyckades. På samma sätt, om den LED motsvarar **AC misslyckas** är tänd, strömförsörjningen misslyckades. 
   
    ![Serverdelen av enheten PCM övervakningsindikatorer](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Bild 2** tillbaka of PCM med övervakningsindikatorer
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |AC-strömavbrott |
   | 2 |Fläkt fel |
   | 3 |Batteri fel |
   | 4 |PCM OK |
   | 5 |DC strömavbrott |
   | 6 |Batteri är felfria |
4. Se följande diagram på baksidan av StorSimple-enheten för att hitta den misslyckade PCM-modulen. PCM 0 är till vänster och PCM 1 till höger. Tabellen nedan beskrivs modulerna.
   
     ![Serverdelen av enhetens primära Höljesmoduler](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Bild 3** baksidan av enheten med plugin-program 
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollenhet 0 |
   | 4 |Kontrollenhet 1 |
5. Stäng av felaktiga PCM och koppla från strömsladd för leverans. Du kan nu ta bort PCM.
6. Rapportelementen låset och serversidan referensens PCM mellan tummen och pekfingret och klämma dem tillsammans för att öppna referensen.
   
    ![Öppna en PCM referens](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Bild 4** att öppna PCM-referens
7. Krama referensen och ta bort PCM.
   
    ![Ta bort enhet PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Bild 5** tar bort PCM

## <a name="install-a-replacement-pcm"></a>Installera en ersättning PCM
Följ dessa instruktioner för att installera en PCM i din StorSimple-enhet. Se till att du har satt extrabatteri modulen innan du installerar att ersätta PCM (gäller endast 764 W PCMs). Läs mer om hur du [ta bort och infoga en extrabatteri modul](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Att installera en PCM
1. Kontrollera att du har rätt ersättningen PCM för den här höljet. Primär höljet måste en 764 W PCM och EBOD-höljet måste en 580 W PCM. Du bör inte försöka använda 580 W PCM i det primära höljet eller 764 W PCM i EBOD-hölje. Följande bild visar var du vill identifiera den här informationen på den etikett som fästs på PCM.
   
    ![Enheten PCM etikett](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Bild 6** PCM etikett
2. Sök efter skador höljet var särskilt uppmärksam på anslutningarna. 
   
   > [!NOTE]
   > **Installera inte modulen om alla connector stift är böjda.**
   > 
   > 
3. Dra modulen till höljet med PCM-referensen i öppet läge.
   
    ![Installera enheten PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Bild 7** installerar PCM
4. Stäng manuellt PCM-referensen. Du bör höra ett klick som talar med referensen spärr.
   
   > [!NOTE]
   > För att säkerställa att PIN-koder för anslutningstjänsten har utövar, kan du försiktigt tug på referensen utan att släppa låset. Om PCM skjuts ut, innebär det att låset stängdes innan anslutningarna engagerade.
   
5. Ansluta strömkablarna till en strömkälla och till PCM.
6. Skydda belastningen befrielse balar.
7. Aktivera PCM.
8. Verifiera att ersättningen lyckades: navigera till din enhet i Azure-portalen för StorSimple Device Manager-tjänsten och sedan till **Inställningar > övervaka > hälsotillstånd för maskinvara**. Under den **delade komponenter**, status för PCM vara grön.
   
   > [!NOTE]
   > Det kan ta några minuter för att ersätta PCM att initiera helt.

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).

