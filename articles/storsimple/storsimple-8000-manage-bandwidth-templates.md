---
title: Hantera bandbreddsmallar för StorSimple 8000-serien | Microsoft-dokument
description: Beskriver hur du hanterar StorSimple bandbreddsmallar, som gör att du kan styra bandbreddsförbrukningen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254903"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Använda Tjänsten StorSimple Device Manager för att hantera StorSimple-bandbreddsmallar

## <a name="overview"></a>Översikt

Med bandbreddsmallar kan du konfigurera användningen av nätverksbandbredd över flera tidsscheman för att nivåa data från StorSimple-enheten till molnet.

Med bandbreddsbegränsningsscheman kan du:

* Ange anpassade bandbreddsscheman beroende på arbetsbelastningens nätverksanvändningar.
* Centralisera hanteringen och återanvända scheman på flera enheter på ett enkelt och smidigt sätt.

> [!NOTE]
> Den här funktionen är endast tillgänglig för StorSimple fysiska enheter (modellerna 8100 och 8600) och inte för StorSimple Cloud Appliances (modellerna 8010 och 8020).


## <a name="the-bandwidth-templates-blade"></a>Bladet Bandbreddsmallar

Bladet **Bandbreddsmallar** har alla bandbreddsmallar för tjänsten i tabellformat och innehåller följande information:

* **Namn** – Ett unikt namn som tilldelats bandbreddsmallen när den skapades.
* **Schema** – Antalet scheman som finns i en viss bandbreddsmall.
* **Används av** – Antalet volymer som använder bandbreddsmallarna.

Du kan också hitta ytterligare information som hjälper dig att konfigurera bandbreddsmallar i:

* [Frågor och svar om bandbreddsmallar](#questions-and-answers-about-bandwidth-templates)
* [Metodtips för bandbreddsmallar](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Lägga till en bandbreddsmall

Gör följande för att skapa en ny bandbreddsmall.

#### <a name="to-add-a-bandwidth-template"></a>Så här lägger du till en bandbreddsmall

1. Gå till tjänsten StorSimple Device Manager, klicka på **Bandbreddsmallar** och klicka sedan på **+ Lägg till bandbreddsmall**.

    ![Klicka på + Lägg till bandbreddsmall](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Gör följande i **mallbladet Lägg till bandbredd:**
   
    1. Ange ett unikt namn för bandbreddsmallen.
    2. Definiera ett bandbreddsschema. Så här skapar du ett schema:
   
        1. Välj de **veckodagar** som schemat är konfigurerat för i listrutan. Du kan välja flera dagar.        
        
        2. Ange en **starttid** i _hh:mm-format._ Det är då schemat börjar.

        3. Ange en **sluttid** i _hh:mm-format._ Det är då schemat kommer att sluta.
      
           > [!NOTE]
           > Överlappande scheman är inte tillåtna. Om start- och sluttiderna resulterar i ett överlappande schema visas ett felmeddelande om detta.

        4. Ange **bandbreddshastighet**. Det här är bandbredden i Megabit per sekund (Mbps) som används av StorSimple-enheten i operationer som involverar molnet (både uppladdningar och nedladdningar). Ange ett tal mellan 1 och 1 000 för det här fältet.

            ![Definiera bandbreddsschema](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Upprepa ovanstående steg för att definiera flera scheman för mallen tills du är klar.

        5. Klicka på **Lägg till** om du vill börja skapa en bandbreddsmall. Den skapade mallen läggs till i listan över bandbreddsmallar.
      

## <a name="edit-a-bandwidth-template"></a>Redigera en bandbreddsmall

Gör följande för att redigera en bandbreddsmall.

### <a name="to-edit-a-bandwidth-template"></a>Så här redigerar du en bandbreddsmall

1. Gå till tjänsten StorSimple Device Manager och klicka på **Bandbreddsmallar**.
2. Välj den mall som du vill ta bort i listan över bandbreddsmallar. Högerklicka och välj **Ta bort**på snabbmenyn .
3. Klicka på **OK**när du uppmanas att bekräfta . Detta bör ta bort bandbreddsmallen. 
4. Listan över bandbreddsmallar uppdateras för att återspegla borttagningen.

> [!NOTE]
> Du kan inte spara ändringarna om det redigerade schemat överlappar ett befintligt schema i bandbreddsmallen som du ändrar.

## <a name="delete-a-bandwidth-template"></a>Ta bort en bandbreddsmall

Gör följande för att ta bort en bandbreddsmall.

#### <a name="to-delete-a-bandwidth-template"></a>Så här tar du bort en bandbreddsmall

1. Gå till tjänsten StorSimple Device Manager och klicka på **Bandbreddsmallar**.
2. Välj den mall som du vill ta bort i listan över bandbreddsmallar. Högerklicka och välj Ta bort på snabbmenyn.
3. Klicka på **OK**när du uppmanas att bekräfta . Detta bör ta bort bandbreddsmallen.
4. Listan över bandbreddsmallar uppdateras för att återspegla borttagningen.

Om mallen används av någon volym(er) kommer du inte att tillåtas att ta bort den. Ett felmeddelande visas om att mallen används. En dialogruta för felmeddelanden visas där du uppmanas att ta bort alla referenser till mallen.

Du kan ta bort alla referenser till mallen genom att öppna sidan **Volymbehållare** och ändra volymbehållare som använder den här mallen så att de använder en annan mall eller använder en anpassad eller obegränsad bandbreddsinställning. När alla referenser har tagits bort kan du ta bort mallen.

## <a name="use-a-default-bandwidth-template"></a>Använda en standardbandbreddsmall

En standardbandbreddsmall tillhandahålls och används av volymbehållare som standard för att framtvinga bandbreddskontroller när du öppnar molnet. Standardmallen fungerar också som en färdig referens för användare som skapar sina egna mallar. Informationen i den här standardmallen är:

* **Namn** – Obegränsade nätter och helger
* **Schema** – Ett enda schema från måndag till fredag som gäller en bandbreddshastighet på 1 Mbit/s mellan 08.00 och 17.00.00 enhetstid. Bandbredden är inställd på Obegränsad för resten av veckan.

Standardmallen kan redigeras. Användningen av den här mallen (inklusive redigerade versioner) spåras.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Skapa en bandbreddsmall hela dagen som startar vid en viss tidpunkt

Följ den här proceduren om du vill skapa ett schema som börjar vid en angiven tid och körs hela dagen. I exemplet börjar schemat klockan 9 på morgonen och pågår till 09:00 nästa morgon. Det är viktigt att notera att start- och sluttiderna för ett visst schema måste finnas både på samma 24-timmarsschema och inte kan sträcka sig över flera dagar. Om du behöver ställa in bandbreddsmallar som sträcker sig över flera dagar måste du använda flera scheman (som visas i exemplet).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Så här skapar du en bandbreddsmall hela dagen

1. Skapa ett schema som börjar klockan 9 på morgonen och pågår till midnatt.
2. Lägg till ett annat schema. Konfigurera det andra schemat så att det körs från midnatt till 09.00 på morgonen.
3. Spara bandbreddsmallen.

Det sammansatta schemat börjar sedan vid en tidpunkt som du väljer och körs hela dagen.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Frågor och svar om bandbreddsmallar

**Q**. Vad händer med bandbreddskontroller när du är mellan schemana? (Ett schema har avslutats och ett annat har inte startat ännu.)

**A**. I sådana fall kommer inga bandbreddskontroller att användas. Detta innebär att enheten kan använda obegränsad bandbredd när du nivåindelar data till molnet.

**Q**. Kan du ändra bandbreddsmallar på en offlineenhet?

**A**. Du kan inte ändra bandbreddsmallar på volymbehållare om motsvarande enhet är offline.

**Q**. Kan du redigera en bandbreddsmall som är associerad med en volymbehållare när de associerade volymerna är offline?

**A**. Du kan ändra en bandbreddsmall som är associerad med en volymbehållare vars volymer är offline. Observera att när volymerna är offline, kommer inga data att nivåindelas från enheten till molnet.

**Q**. Kan du ta bort en standardmall?

**A**. Även om du kan ta bort en standardmall är det ingen bra idé att göra det. Användningen av en standardmall, inklusive redigerade versioner, spåras. Spårningsdata analyseras och används med tiden för att förbättra standardmallen.

**Q**. Hur fastställer du att dina bandbreddsmallar behöver ändras?

**A**. Ett av tecknen på att du behöver ändra bandbreddsmallarna är när du börjar se nätverket sakta ner eller kvävas flera gånger på en dag. Om detta inträffar övervakar du lagrings- och användningsnätverket genom att titta på diagrammen för I/O-prestanda och nätverksdataflöde.

Identifiera tid på dagen och volymbehållarna där nätverksflaskhalsen uppstår från data för nätverksdata. Om detta inträffar när data nivåindelas till molnet (få den här informationen från I/O-prestanda för alla volymbehållare för enhet till moln) måste du ändra bandbreddsmallarna som är associerade med dina volymbehållare.

När de ändrade mallarna används måste du övervaka nätverket igen för betydande svarstider. Om dessa fortfarande finns måste du se över bandbreddsmallarna.

**Q**. Vad händer om flera volymbehållare på enheten har scheman som överlappar varandra men olika gränser gäller för varje?

**A**. Låt oss anta att du har en enhet med 3 volymbehållare. De scheman som är associerade med dessa behållare överlappar varandra helt. För var och en av dessa behållare är de bandbreddsgränser som används 5, 10 respektive 15 Mbit/s. När I/O förekommer på alla dessa behållare samtidigt kan minst 3 bandbreddsgränser tillämpas: i det här fallet delar 5 Mbps eftersom dessa utgående I/O-begäranden samma kö.

## <a name="best-practices-for-bandwidth-templates"></a>Metodtips för bandbreddsmallar

Följ de här metodtipsen för din StorSimple-enhet:

* Konfigurera bandbreddsmallar på enheten så att enheten kan aktivera variabel begränsning av nätverkets dataflöde vid olika tidpunkter på dygnet. Dessa bandbreddsmallar när de används med säkerhetskopieringsscheman kan effektivt utnyttja ytterligare nätverksbandbredd för molnåtgärder under lågtrafik.
* Beräkna den faktiska bandbredd som krävs för en viss distribution baserat på distributionens storlek och det nödvändiga återställningstidens mål (RTO).

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

