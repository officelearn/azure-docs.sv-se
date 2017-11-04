---
title: Hantera dina StorSimple bandbredd mallar | Microsoft Docs
description: "Beskriver hur du hanterar StorSimple bandbredd mallar, vilket gör att du kan styra bandbreddsanvändning."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0027b90e-91a5-437d-9bd0-06b05674aa5f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: alkohli
ms.openlocfilehash: df3ae8bf775370432b3648459a7c942afe69fb17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Använda StorSimple Manager-tjänsten för att hantera StorSimple bandbredd mallar
## <a name="overview"></a>Översikt
Bandbredd-mallar kan du konfigurera användningen av nätverksbandbredd mellan flera tid på dagen scheman för tjänstnivån data från StorSimple-enheten till molnet.

Med bandbreddsbegränsning scheman kan du:

* Ange anpassade bandbredd scheman beroende på arbetsbelastningen nätverket användningsområden.
* Centralisera hanteringen och återanvända scheman över flera enheter på ett enkelt och smidigt sätt.

> [!NOTE]
> Den här funktionen är tillgänglig endast för fysiska StorSimple-enheter och inte för virtuella enheter.
> 
> 

Alla mallar för bandbredd för din tjänst visas i tabellformat och innehåller följande information:

* **Namnet** – ett unikt namn som tilldelats bandbredd mallen när den skapades.
* **Schemat** – antalet scheman som finns i en viss bandbreddsmall.
* **Används av** – antalet volymer med hjälp av mallar för bandbredd.

Du kan använda StorSimple Manager-tjänsten **konfigurera** sida i den klassiska Azure portalen för att hantera bandbredd mallar.

Du kan också hitta mer information för att konfigurera bandbredd mallar i:

* Frågor och svar om bandbredd mallar
* Metodtips för bandbredd mallar

## <a name="add-a-bandwidth-template"></a>Lägg till en bandbreddsmall
Utför följande steg för att skapa en ny bandbreddsmall för.

#### <a name="to-add-a-bandwidth-template"></a>Att lägga till en bandbreddsmall
1. På StorSimple Manager-tjänsten **konfigurera** klickar du på **lägga till eller redigera bandbreddsmall**.
2. I den **Lägg till/redigera Bandbreddsmall** dialogrutan:
   
   1. Från den **mallen** listrutan, Välj **Skapa nytt** att lägga till en ny bandbreddsmall för.
   2. Ange ett unikt namn för mallen för bandbredd.
3. Definiera en **bandbredd schema**. Skapa ett schema:
   
   1. Välj dagar i veckan som schemat har konfigurerats för den nedrullningsbara listan. Du kan välja flera dagar genom att markera kryssrutorna före respektive dagar i listan.
   2. Välj den **hela dagen** om schemat tillämpas för hela dagen. När det här alternativet är markerat kan du inte längre ange en **starttid** eller en **sluttid**. Schemalägg körs från 12:00:00 till 11:59 PM.
   3. I listrutan, väljer du en **starttid**. Detta är när schemat börjar.
   4. I listrutan, väljer du en **sluttid**. Detta är när schemat stoppas.
      
      > [!NOTE]
      > Överlappande scheman tillåts inte. Om start- och sluttider resulterar i ett överlappande schema, visas ett felmeddelande för detta.
      > 
      > 
   5. Ange den **bandbredd hastighet**. Detta är bandbredd i megabit per sekund (Mbps) som används av din StorSimple-enhet i åtgärder som rör molnet (överföringar och hämtningsbara filer). Ange ett tal mellan 1 och 1000 för det här fältet.
   6. Klicka på kryssikonen ![kryssikonen](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Den mall som du har skapat kommer att läggas till i listan över mallar bandbredd på tjänsten **konfigurera** sidan.
      
      ![Skapa en ny bandbreddsmall](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)
4. Klicka på **spara** längst ned på sidan och klicka sedan på **Ja** när du uppmanas att bekräfta. Ändringar i konfigurationen som du har gjort sparas.

## <a name="edit-a-bandwidth-template"></a>Redigera en bandbreddsmall
Utför följande steg om du vill redigera en bandbreddsmall.

### <a name="to-edit-a-bandwidth-template"></a>Så här redigerar du en bandbreddsmall
1. Klicka på **lägga till eller redigera bandbreddsmall**.
2. I den **Lägg till/redigera Bandbreddsmall** dialogrutan:
   
   1. Från den **mallen** listrutan väljer du en befintlig bandbreddsmall som du vill ändra.
   2. Slutför ändringarna. (Du kan ändra någon av de befintliga inställningarna.)
   3. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Den ändrade mallen i listan över bandbredd mallar visas på sidan Konfigurera service.
3. Om du vill spara ändringarna klickar du på **spara** längst ned på sidan. Klicka på **Ja** när du uppmanas att bekräfta.

> [!NOTE]
> Du kommer inte att spara ändringarna om du redigerade schemat överlappar ett befintligt schema i mallen bandbredd som du ändrar.
> 
> 

## <a name="delete-a-bandwidth-template"></a>Ta bort en bandbreddsmall
Utför följande steg för att ta bort en bandbreddsmall.

#### <a name="to-delete-a-bandwidth-template"></a>Ta bort en bandbreddsmall
1. Välj den mall som du vill ta bort i listan tabular av bandbredd mallar för din tjänst. En borttagningsikonen (**x**) visas till höger extrema om den valda mallen. Klicka på den **x** ikonen Ta bort mallen.
2. Du uppmanas att bekräfta. Klicka på **OK** att fortsätta.

Om mallen används av alla volymerna, kommer du inte att kunna ta bort den. Du ser ett felmeddelande om att mallen används. En dialogrutan med felmeddelandet visas som talar om att alla referenser till mallen bör tas bort.

Du kan ta bort alla referenser till mallen genom att öppna den **Volymbehållare** sidan och ändra volymbehållarna som använder den här mallen så att de använder en annan mall eller använda en bandbreddsinställning för anpassade eller obegränsad. När alla referenser har tagits bort, kan du ta bort mallen.

## <a name="use-a-default-bandwidth-template"></a>Använda en standardmall för bandbredd
En standardmall för bandbredd tillhandahålls och används av volymbehållare som standard för att använda bandbreddskontroller vid åtkomst till molnet. Standardmallen fungerar också som en referens som är redo för användare skapar egna mallar. Information om den här standardmallen är:

* **Namnet** – obegränsade kvällar och helger
* **Schemat** – ett enda schema från måndag till fredag som gäller en bandbredd andel 1 Mbit/s mellan 8: 00 och 17: 00 tid. Bandbredden är inställd på obegränsad för resten av veckan.

Standardmallen kan redigeras. Användning av den här mallen (inklusive redigerade versioner) spåras.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Skapa en varar bandbreddsmall som börjar vid en angiven tid
Gör så här om du vill skapa ett schema som startar vid en viss tidpunkt och kör hela dagen. I det här exemplet schemat startar på 9: 00 på morgonen och kör tills 9: 00 nästa morgon. Det är viktigt att notera att start- och sluttider för ett visst schema måste båda finnas på samma schema i 24-timmarsformat och kan sträcka sig över flera dagar. Om du behöver konfigurera bandbredd mallar som sträcker sig över flera dagar, behöver du använda flera scheman (som visas i exemplet).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Skapa en bandbreddsmall för varar
1. Skapa ett schema som börjar vid 9: 00 på morgonen och kör till midnatt.
2. Lägg till ett annat schema. Konfigurera andra schemat ska köras från midnatt tills 9: 00 på morgonen.
3. Spara mallen bandbredd.

Sammansatt schema kommer sedan starta vid ett tillfälle väljer och kör varar.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Frågor och svar om bandbredd mallar
**Q**. Vad händer med bandbreddskontroller när du är between scheman? (Ett schema har avslutats och en annan har inte startat ännu.)

**EN**. I sådana fall kommer inga bandbreddskontroller användas. Det innebär att enheten kan använda obegränsad bandbredd när skiktning data till molnet.

**Q**. Kan du ändra bandbredd mallar på en offline-enhet?

**EN**. Du kommer inte att kunna ändra bandbredd mallar på volymer behållare om motsvarande enheten är offline.

**Q**. Kan du redigera en bandbreddsmall som är associerade med en volymbehållare när tillhörande volymer är offline?

**EN**. Du kan ändra en bandbreddsmall som är associerade med en volymbehållare vars volymer som är offline. Observera att när volymer är offline, inga data inte kommer nivåindelas från enheten till molnet.

**Q**. Kan du ta bort en standardmall?

**EN**. Även om du kan ta bort en standardmall, är du inte göra det. Användning av en standardmall, inklusive redigerade versionerna spåras. Spårningsdata analyseras och under loppet av tid används för att förbättra standardmallen.

**Q**. Hur vet du att mallarna bandbredd som behöver ändras?

**EN**. En av tecken som du behöver ändra bandbredd mallar är när du startar ser nätverket långsam eller strypning flera gånger under en dag. Om det händer kan du övervaka nätverkets lagrings- och användningsdata genom att studera diagrammen i/o-prestanda och dataflödet i nätverket.

Identifiera tid på dagen och volymbehållarna som inträffar flaskhalsar i nätverket från genomströmning nätverksdata. Om detta händer när data är att nivåer till molnet (fås från i/o-prestanda för alla volymbehållare för enheten till molnet), måste du ändra mallarna bandbredd som är associerade med din volymbehållare.

Efter att ändrade mallar används behöver du övervaka igen efter betydande fördröjningar i nätverket. Om de fortfarande finns måste att se över mallarna bandbredd.

**Q**. Vad händer om flera volymbehållare på enheten har schemalägger som överlappar varandra men olika gränser gäller för varje?

**EN**. Anta att du har en enhet med 3 volymbehållare. Scheman som är associerade med de här behållarna helt överlappar varandra. För var och en av de här behållarna är bandbreddsgränser som används 5, 10, och 15 Mbit/s. När I/o uppstår på alla dessa behållare på samma gång, minst 3 bandbreddsgränser kan användas: i det här fallet 5 Mbit/s som dessa utgående i/o-begäranden delar samma kö.

## <a name="best-practices-for-bandwidth-templates"></a>Metodtips för bandbredd mallar
Följ dessa rekommenderade säkerhetsmetoderna för din StorSimple-enhet:

* Konfigurera bandbredd mallar på enheten för att aktivera variabeln begränsning av genomströmningen i nätverket för enheten vid olika tidpunkter på dagen. Mallarna bandbredd när det används med scheman för säkerhetskopiering kan effektivt utnyttja nätverkets bandbredd för molnet åtgärder vid låg belastning.
* Beräkna den faktiska bandbredd som krävs för en viss distribution baserat på storleken på distributionen och den nödvändiga recovery tid mål för Återställningstid.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

