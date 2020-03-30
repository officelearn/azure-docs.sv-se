---
title: Byt batteri på Microsoft Azure StorSimple 8000-seriens enhet
description: Beskriver hur du tar bort, byter ut och underhåller batterimodulen för säkerhetskopiering på Din StorSimple-enhet.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255020"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Ersätta modulen för extrabatteriet på StorSimple-enheten

## <a name="overview"></a>Översikt
Den primära höljet Power and Cooling Module (PCM) på din Microsoft Azure StorSimple-enhet har ett extra batteripaket. Det här paketet ger ström så att StorSimple-enheten kan spara data om det finns förlust av växelström till den primära höljet. Detta batteripaket kallas backup *batterimodul*. Batterimodulen för säkerhetskopiering finns endast för den primära höljet i StorSimple-enheten (EBOD-höljet innehåller ingen batterimodul för säkerhetskopiering).

I den här självstudien beskrivs hur du:

* Ta bort batterimodulen för säkerhetskopiering
* Installera en ny batterimodul för säkerhetskopiering
* Underhåll batterimodulen för säkerhetskopiering

> [!IMPORTANT]
> Innan du tar bort och byter ut en batterimodul för säkerhetskopiering bör du läsa säkerhetsinformationen i [ersättningen för inledning till StorSimple-maskinvarukomponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Ta bort batterimodulen för säkerhetskopiering
Batterimodulen för säkerhetskopiering för StorSimple-enheten är en fältutbytbar enhet. Innan den installeras i PCM ska batterimodulen förvaras i originalförpackningen. Utför följande steg för att ta bort reservbatteriet.

#### <a name="to-remove-the-backup-battery-module"></a>Så här tar du bort batterimodulen för säkerhetskopiering
1. Gå till tjänstbladet För StorSimple Device Manager i Azure-portalen. Gå till **Enheter** och välj sedan enheten i listan över enheter. Navigera till **Övervaka** > **maskinvaruhälsa**. Under **Delade komponenter**tittar du på batteriets status.
2. Identifiera den PCM där batteriet har misslyckats. Bild 1 visar baksidan av StorSimple-enheten.
   
    ![Bakplan av enhetens primära kapslingsmoduler](./media/storsimple-battery-replacement/IC740994.png)
   
    **Bild 1** Baksidan av den primära enheten som visar PCM- och styrenhetsmoduler
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollant 0 |
   | 4 |Kontrollant 1 |
   
    Som framgår av nummer 3 i bild 2 ska övervakningsindikatorn på PCM 0 som motsvarar **batterifel** tändas.
   
    ![Bakplan av enhet PCM övervakningsindikator lysdioder](./media/storsimple-battery-replacement/IC740992.png)
   
    **Bild 2** Baksidan av PCM som visar övervakningsindikatorlysdioderna
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |Strömavbrott |
   | 2 |Fläktfel |
   | 3 |Batterifel |
   | 4 |PCM OK |
   | 5 |Dc strömavbrott |
   | 6 |Batteriet är hälsosamt |
3. Om du vill ta bort PCM med ett misslyckat batteri följer du stegen i [Ta bort en PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. När PCM har tagits bort lyfter du och roterar batterimodulhandtaget uppåt enligt följande bild och drar upp det för att ta bort batteriet.
   
    ![Ta bort batteri från PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Bild 3** Ta bort batteriet från PCM
5. Placera modulen i den fältfördelbara enhetsförpackningen.
6. Returnera den defekta enheten till Microsoft för korrekt service och hantering.

## <a name="install-a-new-backup-battery-module"></a>Installera en ny batterimodul för säkerhetskopiering
Utför följande steg för att installera ersättningsbatterimodulen i PCM i den primära höljet på Din StorSimple-enhet.

#### <a name="to-install-the-battery-module"></a>Så här installerar du batterimodulen
1. Placera batterimodulen för säkerhetskopiering i rätt riktning i PCM.
2. Tryck ner batterimodulens handtag hela vägen för att placera kontakten.
3. Byt ut PCM i det primära höljet genom att följa riktlinjerna i [Ersätt en ström- och kylmodul på din StorSimple-enhet](storsimple-8000-power-cooling-module-replacement.md).
4. När ersättningen är klar går du till enheten och går sedan till **Övervaka** > **maskinvaruhälsa** i Azure-portalen. Kontrollera batteriets status för att kontrollera att installationen lyckades. En grön status indikerar att batteriet är felfritt.

## <a name="maintain-the-backup-battery-module"></a>Underhåll batterimodulen för säkerhetskopiering
I StorSimple-enheten ger batterimodulen för säkerhetskopiering ström till handkontrollen under en händelse av strömavbrott. Det gör det möjligt för StorSimple-enheten att spara viktiga data innan den stängs av på ett kontrollerat sätt. Med två fulladdade batterier i PCM kan systemet hantera två på varandra följande förlusthändelser.

I Azure-portalen anger **maskinvaruhälsan** under **bladet Övervakar** om batteriet inte fungerar eller att uttjänt närmar sig. Batteristatusen indikeras med **batteri i PCM 0** eller **Batteri i PCM 1** under **Delade komponenter**. Detta blad kommer att visa ett **DEGRADERAT** tillstånd för uttjänt närmar sig, och **misslyckades** för slutet av livet nåtts.

> [!NOTE]
> Batteriet kan rapportera **misslyckades** när det helt enkelt behöver laddas.


Om **tillståndet DEGRADED** visas rekommenderar vi följande åtgärd:

* Systemet kan ha drabbats av strömavbrott nyligen eller så kan batterierna genomgå periodiskt underhåll. Observera systemet i 12 timmar innan du fortsätter.
  
  * Om tillståndet fortfarande är **DEGRADERAT** efter 12 timmars kontinuerlig anslutning till nätström med styrenheter och PCM igång, måste batteriet bytas ut. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för en batterimodul för säkerhetskopiering av säkerhetskopiering.
  * Om tillståndet blir OK efter 12 timmar är batteriet i drift och det behövde bara en underhållsladdning.
* Om det inte har skett någon tillhörande förlust av växelström och PCM är påslagen och ansluten till nätström måste batteriet bytas ut. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att beställa en batterimodul för säkerhetskopiering.

> [!IMPORTANT]
> Kassera det felaktiga batteriet enligt nationella och regionala bestämmelser.

## <a name="next-steps"></a>Nästa steg
Läs mer om byte av [Maskinvarukomponent i StorSimple](storsimple-8000-hardware-component-replacement.md).

