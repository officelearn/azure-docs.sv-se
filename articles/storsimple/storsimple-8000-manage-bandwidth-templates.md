---
title: Hantera bandbreddsmallar för StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du hanterar StorSimple bandbreddsmallar, där du kan styra bandbreddsanvändning.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699485"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Använda StorSimple Device Manager-tjänsten för att hantera StorSimple bandbreddsmallar

## <a name="overview"></a>Översikt

Bandbreddsmallar kan du konfigurera användningen av nätverksbandbredd mellan flera scheman för tid på dagen till tier data från StorSimple-enhet till molnet.

Med bandbreddsbegränsning scheman kan du:

* Definiera anpassade bandbredd beroende på arbetsbelastningen nätverk användningar.
* Centralisera hanteringen och återanvända scheman över flera enheter på ett enkelt och smidigt sätt.

> [!NOTE]
> Den här funktionen är tillgänglig endast för fysiska StorSimple-enheter (modeller 8100 och 8600) och inte för StorSimple Cloud Appliances (modeller 8010 och 8020).


## <a name="the-bandwidth-templates-blade"></a>Bladet för mallar för bandbredd

Den **bandbreddsmallar** bladet har alla bandbreddsmallar för din tjänst i tabellformat, och innehåller följande information:

* **Namn på** – ett unikt namn som tilldelas bandbreddsmallen när den skapades.
* **Schema** – antalet scheman som tillhör en viss bandbredd-mall.
* **Används av** – antalet volymer med hjälp av bandbreddsmallar.

Du kan också hitta ytterligare information för att konfigurera bandbreddsmallar i:

* [Frågor och svar om bandbreddsmallar](#questions-and-answers-about-bandwidth-templates)
* [Metodtips för bandbreddsmallar](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Lägg till en bandbreddsmall

Utför följande steg för att skapa en ny bandbreddsmall för.

#### <a name="to-add-a-bandwidth-template"></a>Att lägga till en bandbreddsmall

1. Gå till StorSimple Device Manager-tjänsten, klicka på **bandbreddsmallar** och klicka sedan på **+ Lägg till bandbreddsmall**.

    ![Klicka på + Lägg till bandbreddsmall](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. I den **Lägg till bandbreddsmall** bladet gör följande:
   
    1. Ange ett unikt namn för bandbreddsmallen för.
    2. Definiera ett schema för bandbredd. Skapa ett schema:
   
        1. Från listrutan, väljer den **dagar** i veckan schemat är konfigurerad för. Du kan välja flera dagar.        
        
        2. Ange en **starttid** i _hh: mm_ format. Detta är när påbörjas schemat.

        3. Ange en **sluttid** i _hh: mm_ format. Detta är när schemat avbryts.
      
           > [!NOTE]
           > Överlappande scheman tillåts inte. Om start- och sluttider resulterar i ett överlappande schema, visas ett felmeddelande för detta.

        4. Ange den **Bandbreddshastighet**. Det här är bandbredden i megabit per sekund (Mbit/s) som används av din StorSimple-enhet i åtgärder som rör molnet (överföringar och nedladdningar). Ange ett tal mellan 1 och 1 000 för det här fältet.

            ![Definiera bandbredd schema](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Upprepa stegen ovan för att definiera flera scheman för mallen tills du är klar.

        5. Klicka på **Lägg till** att börja skapa en bandbreddsmall. Mallen som du har skapat läggs till i listan över bandbreddsmallar.
      

## <a name="edit-a-bandwidth-template"></a>Redigera en bandbreddsmall

Utför följande steg om du vill redigera en bandbreddsmall.

### <a name="to-edit-a-bandwidth-template"></a>Så här redigerar du en bandbreddsmall

1. Gå till StorSimple Device Manager-tjänsten och klicka på **bandbreddsmallar**.
2. Välj den mall som du vill ta bort i listan över bandbreddsmallar. Högerklicka och välj snabbmenyn **ta bort**.
3. När du uppmanas att bekräfta klickar du på **OK**. Detta bör ta bort bandbreddsmallen. 
4. Lista över bandbredd mallar uppdateringar borttagningen.

> [!NOTE]
> Du kan inte spara dina ändringar om du redigerade schemat överlappar ett befintligt schema i den bandbreddsmallen som du ändrar.

## <a name="delete-a-bandwidth-template"></a>Ta bort en bandbreddsmall

Utför följande steg för att ta bort en bandbreddsmall.

#### <a name="to-delete-a-bandwidth-template"></a>Att ta bort en bandbreddsmall

1. Gå till StorSimple Device Manager-tjänsten och klicka på **bandbreddsmallar**.
2. Välj den mall som du vill ta bort i listan över bandbreddsmallar. Högerklicka och välj Ta bort på snabbmenyn.
3. När du uppmanas att bekräfta klickar du på **OK**. Detta bör ta bort bandbreddsmallen.
4. Lista över bandbredd mallar uppdateringar borttagningen.

Om mallen används av alla volymer, kommer du inte att kunna ta bort den. Du kan se ett felmeddelande om att mallen är i användning. En dialogrutan med felmeddelandet visas som talar om att alla referenser till mallen bör tas bort.

Du kan ta bort alla referenser till mallen genom att öppna den **Volymbehållare** sidan och ändra de volymbehållare som använder den här mallen så att de använder en annan mall eller använda en anpassad eller obegränsad bandbreddsinställning. När alla referenser har tagits bort, kan du ta bort mallen.

## <a name="use-a-default-bandwidth-template"></a>Använda en standardmall för bandbredd

En standardmall för bandbredd tillhandahålls och används av volymbehållare som standard för att framtvinga kontrollerna för nätverksband vid åtkomst till molnet. Standardmallen fungerar också som en referens som är redo för användare som skapar sina egna mallar. Information om den här standardmallen är:

* **Namn på** – obegränsat nätter och helger
* **Schema** – ett enda schema från måndag till fredag som gäller en bandbreddshastighet på 1 Mbit/s mellan 8: 00 och 17: 00 enhetens tid. Bandbredden som är inställd på obegränsad för resten av veckan.

Standardmallen kan redigeras. Användningen av den här mallen (inklusive redigerade versionerna) spåras.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Skapa en varar hela bandbreddsmallen som börjar vid en angiven tid

Följ den här proceduren om du vill skapa ett schema som startar vid en viss tidpunkt och kör hela dagen. I det här exemplet schemat börjar kl. 9 på morgonen och körs fram till den 9: 00 nästa morgon. Det är viktigt att notera att start- och sluttider för ett visst schema måste båda finnas på samma 24-timmars schema och kan inte omfatta flera dagar. Om du vill konfigurera bandbreddsmallar som sträcker sig över flera dagar kommer du behöva använda flera scheman (som visas i det här exemplet).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Skapa en varar hela bandbreddsmall

1. Skapa ett schema som startar kl. 9 på morgonen och kör till midnatt.
2. Lägg till ett annat schema. Konfigurera andra schemat på körning från midnatt fram till 9: 00 på morgonen.
3. Spara bandbreddsmallen.

Sammansatt schema kommer sedan att starta i taget valfri och kör varar hela.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Frågor och svar om bandbreddsmallar

**FRÅGOR OCH**. Vad händer med bandbreddskontroller när du är mellan scheman? (Ett schema har avslutats och en annan har inte startat ännu.)

**EN**. I sådana fall kan ska inga bandbreddskontroller användas. Det innebär att enheten kan använda obegränsad bandbredd när lagringsnivåer data till molnet.

**FRÅGOR OCH**. Kan du ändra bandbreddsmallar på en offline-enhet?

**EN**. Du kommer inte att kunna ändra bandbreddsmallar på volymer behållare om motsvarande enheten är offline.

**FRÅGOR OCH**. Kan du redigera en bandbreddsmall som är associerade med en volymbehållare när tillhörande volymer är offline?

**EN**. Du kan ändra en bandbreddsmall som är associerade med en volymbehållare vars volymer är offline. Observera att när volymer är offline kan inga data kommer att vara nivåindelad från enheten till molnet.

**FRÅGOR OCH**. Kan du ta bort en standardmall?

**EN**. Även om du kan ta bort en standardmall, men det är inte en bra idé att göra detta. Användningen av en standardmall, inklusive redigerade versionerna spåras. Spårning av data analyseras och under loppet av tid används för att förbättra standardmallen.

**FRÅGOR OCH**. Hur vet du att din bandbreddsmallar behöver ändras?

**EN**. En av loggar att du behöver ändra mallarna som bandbredd är när du startar ser nätverket långsammare eller strypning flera gånger under en dag. Om det händer kan du övervaka nätverk för lagring och användning genom att studera diagrammen i/o-prestanda och dataflöde i nätverket.

Identifiera tid på dagen och de volymbehållare som nätverk flaskhalsen uppstår från dataflöde nätverksdata. Om detta händer när är som nivåindelade data till molnet (hämta informationen från i/o-prestanda för alla volymbehållare för enheten till molnet), måste du ändra mallarna bandbredd som är associerade med din volymbehållare.

När de ändrade mallarna är i användning, behöver övervaka igen efter betydande fördröjningar i nätverket. Om de fortfarande finns måste du gå tillbaka till din bandbreddsmallar.

**FRÅGOR OCH**. Vad händer om flera volymbehållare på enheten har schemalägger som överlappar varandra men olika begränsningar gäller för var och en?

**EN**. Anta att du har en enhet med 3 volymbehållare. Scheman som är associerade med de här behållarna helt överlappar varandra. För var och en av de här behållarna är gränser för bandbredd som används 5, 10, 15 Mbit/s respektive. När i/o sker på alla dessa behållare på samma gång, minst 3 bandbreddsgränserna kan tillkomma: i det här fallet 5 Mbit/s som dessa utgående i/o-begäranden dela samma kö.

## <a name="best-practices-for-bandwidth-templates"></a>Metodtips för bandbreddsmallar

Följ dessa rekommenderade säkerhetsmetoderna för din StorSimple-enhet:

* Konfigurera bandbreddsmallar på enheten för att aktivera variabeln begränsning av nätverkets genomflöde av enheten vid olika tidpunkter på dagen. Dessa bandbreddsmallar när det används med scheman för säkerhetskopiering kan effektivt utnyttja ytterligare nätverksbandbredd molnanvändning vid låg belastning.
* Beräkna den faktiska bandbredd som krävs för en viss distribution baserat på storleken på distributionen och den nödvändiga återställningstiden (RTO).

## <a name="next-steps"></a>Nästa steg

Läs mer om [med StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

