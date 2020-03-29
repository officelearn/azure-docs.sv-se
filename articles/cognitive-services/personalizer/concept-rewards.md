---
title: Belöningspoäng - Personalizer
description: Belöningspoängen anger hur väl anpassningsvalet, RewardActionID, resulterade för användaren. Värdet på belöningspoängen bestäms av din affärslogik, baserat på observationer av användarbeteende. Personalizer tränar sina maskininlärningsmodeller genom att utvärdera belöningarna.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219372"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Belöningspoäng indikerar framgång för personalisering

Belöningspoängen anger hur väl anpassningsvalet, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), resulterade för användaren. Värdet på belöningspoängen bestäms av din affärslogik, baserat på observationer av användarbeteende.

Personalizer tränar sina maskininlärningsmodeller genom att utvärdera belöningarna.

Lär dig [hur](how-to-settings.md#configure-rewards-for-the-feedback-loop) du konfigurerar standardbelöningspoängen i Azure-portalen för din Personalizer-resurs.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Använd Belönings-API för att skicka belöningspoäng till Personalizer

Belöningar skickas till Personalizer av [belönings-API:et.](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) Vanligtvis är en belöning ett tal från 0 till 1. En negativ belöning, med värdet -1, är möjlig i vissa scenarier och bör endast användas om du har erfarenhet av förstärkningsinlärning (RL). Personalizer tränar modellen för att uppnå högsta möjliga summa belöningar över tiden.

Belöningar skickas efter att användarbeteendet har inträffat, vilket kan vara dagar senare. Den maximala tiden som Personalizer väntar tills en händelse anses ha någon belöning eller en standardbelöning har konfigurerats med [väntetiden för belöning](#reward-wait-time) i Azure-portalen.

Om belöningspoängen för en händelse inte har tagits emot inom **väntetiden för belöning**kommer **standardbelöningen** att tillämpas. Vanligtvis är **[standardbelöningen](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** konfigurerad till noll.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Beteenden och data att tänka på för belöningar

Tänk på dessa signaler och beteenden för kontexten för belöningspoängen:

* Direkt användarinmatning för förslag när det gäller alternativ ("Menar du X?").
* Sessionslängd.
* Tid mellan sessionerna.
* Sentimentanalys av användarens interaktioner.
* Direkta frågor och mini undersökningar där bot ber användaren om feedback om användbarhet, noggrannhet.
* Svar på aviseringar eller fördröjning för att svara på aviseringar.

## <a name="composing-reward-scores"></a>Komponera belöningspoäng

En belöningspoäng måste beräknas i din affärslogik. Poängen kan representeras som:

* Ett enda nummer som skickas en gång
* En poäng skickas omedelbart (till exempel 0,8) och en extra poäng skickas senare (vanligtvis 0,2).

## <a name="default-rewards"></a>Standardbelöningar

Om ingen belöning tas emot inom [förmånsväntetiden](#reward-wait-time), varaktigheten sedan rank-samtalet, tillämpar Personalizer implicit **standardbelöningen** på den rankningshändelsen.

## <a name="building-up-rewards-with-multiple-factors"></a>Bygga upp belöningar med flera faktorer

För effektiv personalisering kan du bygga upp belöningspoängen baserat på flera faktorer.

Du kan till exempel använda dessa regler för att anpassa en lista med videoinnehåll:

|Användarens beteende|Partiellt poängvärde|
|--|--|
|Användaren klickade på det översta objektet.|+0,5 belöning|
|Användaren öppnade det faktiska innehållet i det objektet.|+0,3 belöning|
|Användaren tittade på 5 minuter av innehållet eller 30%, beroende på vilket som är längst.|+0,2 belöning|
|||

Du kan sedan skicka den totala belöningen till API: et.

## <a name="calling-the-reward-api-multiple-times"></a>Anropa belönings-API:et flera gånger

Du kan också anropa belönings-API:et med samma händelse-ID och skicka olika belöningspoäng. När Personalizer får dessa belöningar avgörs den slutliga belöningen för den händelsen genom att samla dem enligt definitionen i Personalizer-konfigurationen.

Aggregeringsvärden:

*  **Första**: Tar den första belöningspoängen som mottagits för evenemanget och kasserar resten.
* **Summa**: Tar alla belöningspoäng som samlats in för eventId och lägger till dem tillsammans.

Alla belöningar för ett evenemang, som tas emot efter **belöningsväntan,** kasseras och påverkar inte utbildningen av modeller.

Genom att lägga upp belöningspoäng kan din slutliga belöning vara utanför det förväntade poängintervallet. Detta kommer inte att göra tjänsten misslyckas.

## <a name="best-practices-for-calculating-reward-score"></a>Bästa praxis för beräkning av belöningspoäng

* **Tänk på verkliga indikatorer på framgångsrik personalisering:** Det är lätt att tänka i termer av klick, men en bra belöning är baserad på vad du vill att dina användare att *uppnå* i stället för vad du vill att folk ska *göra*.  Givande på klick kan till exempel leda till att du väljer innehåll som är klickbenäget.

* **Använd en belöning poäng för hur bra personalisering fungerade:** Anpassa en film förslag skulle förhoppningsvis resultera i att användaren tittar på filmen och ger det ett högt betyg. Eftersom filmen betyg beror förmodligen på många saker (kvaliteten på skådespeleri, stämningen hos användaren), är det inte en bra belöning signal för hur väl *personalisering* fungerade. Användaren tittar på de första minuterna av filmen, men kan vara en bättre signal om personalisering effektivitet och skicka en belöning på 1 efter 5 minuter kommer att vara en bättre signal.

* **Belöningar gäller endast för RewardActionID:** Personalizer tillämpar belöningarna för att förstå effekten av åtgärden som anges i RewardActionID. Om du väljer att visa andra åtgärder och användaren klickar på dem, bör belöningen vara noll.

* **Överväg oavsiktliga konsekvenser:** Skapa belöningsfunktioner som leder till ansvarsfulla resultat med [etik och ansvarsfull användning.](ethics-responsible-use.md)

* **Använd inkrementella belöningar:** Genom att lägga till partiella belöningar för mindre användarbeteenden hjälper Personalizer att uppnå bättre belöningar. Denna inkrementella belöning gör att algoritmen vet att den kommer närmare att engagera användaren i det slutliga önskade beteendet.
    * Om du visar en lista med filmer kan du avgöra om användaren håller muspekaren över den första ett tag för att se mer information. Beteendet kan räknas med en belöningspoäng på 0,1.
    * Om användaren öppnade sidan och sedan avslutade, kan belöningspoängen vara 0,2.

## <a name="reward-wait-time"></a>Väntetid för belöning

Personalizer kommer att korrelera informationen i ett Rank-samtal med belöningarna som skickas i belöningssamtal för att träna modellen. Dessa kan komma vid olika tidpunkter. Personalizer väntar en begränsad tid, med början när Rank-samtalet inträffade, även om Rank-samtalet gjordes som en inaktiv händelse och aktiverades senare.

Om **väntetiden för belöning** går ut och det inte har funnits någon belöningsinformation, tillämpas en standardbelöning på den händelsen för träning. Den maximala väntetiden är 6 dagar.

## <a name="best-practices-for-reward-wait-time"></a>Bästa praxis för belöning väntetid

Följ dessa rekommendationer för bättre resultat.

* Gör belöningstiden så kort du kan, samtidigt som du lämnar tillräckligt med tid för att få feedback från användarna.

* Välj inte en varaktighet som är kortare än den tid som behövs för att få feedback. Till exempel, om några av dina belöningar kommer in efter att en användare har sett 1 minut av en video, bör experimentlängden vara minst dubbelt så hög.

## <a name="next-steps"></a>Nästa steg

* [Kunskapsförmedling](concepts-reinforcement-learning.md)
* [Prova Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Prova belönings-API:et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
