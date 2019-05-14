---
title: Utmärkelse poäng - Personalizer
titleSuffix: Azure Cognitive Services
description: Trafik-poäng indikerar hur väl personanpassning valet, RewardActionID, resulterade för användaren. Värdet för ersättning poängen bestäms av din affärslogik som baseras på observationer av användarbeteende. Personalizer träna dess machine learning-modeller genom att utvärdera fördelarna.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: 536aad0fac4e833cd9a30bad2cfd10e25b0f1300
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607079"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Utmärkelse resultat anger framgången för anpassning

Trafik-poäng indikerar hur väl personanpassning valet, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/rank#rankresponse), resulterade för användaren. Värdet för ersättning poängen bestäms av din affärslogik som baseras på observationer av användarbeteende.

Personalizer träna dess maskininlärningsmodeller genom att utvärdera fördelarna. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Använd utmärkelse API för att skicka trafik poäng till Personalizer

Belöningar som ska skickas till Personalizer genom den [utmärkelse API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Ersättning är ett tal mellan 1 och 1. Personalizer träna modellen för att uppnå högsta möjliga summan av fördelarna med tiden.

Belöningar skickas när användarnas beteende har skett, vilket kan vara dagar senare. Längsta tid som Personalizer ska vänta tills en händelse ska anses ha ingen ersättning eller standard ersättning har konfigurerats med den [utmärkelse väntetid](#reward-wait-time) i Azure-portalen.

Om trafik poängen för en händelse inte har tagits emot inom den **utmärkelse väntetid**, kommer **standard utmärkelse** kommer att tillämpas. Normalt den **[standard utmärkelse](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** är konfigurerad för att vara noll.

## <a name="composing-reward-scores"></a>Skapa utmärkelse poäng

En utmärkelse poäng måste beräknas på din affärslogik. Poängen kan representeras:

* Ett enda tal skickas en gång 
* En poäng skickas omedelbart (till exempel 0,8) och en ytterligare poäng skickas senare (vanligtvis 0,2).

## <a name="default-rewards"></a>Standard belöningar

Om ingen ersättning tas emot inom den [utmärkelse väntetid](#reward-wait-time), varaktighet sedan rangordningen anropa, Personalizer implicit gäller den **standard utmärkelse** på rangordnas händelsen.

## <a name="building-up-rewards-with-multiple-factors"></a>Bygga upp fördelarna med flera faktorer  

För effektiv anpassning som du kan bygga upp trafik-poäng (ett tal mellan 1 och 1) baserat på flera faktorer. 

Du kan till exempel använda dessa regler för att anpassa en lista över videoinnehåll:

|Användarbeteende|Partiell poäng värde|
|--|--|
|Användaren har klickat på det översta objektet.|+0.5 utmärkelse|
|Användaren öppnade det faktiska innehållet om det här objektet.|+0.3 utmärkelse|
|Användaren tittade på 5 minuter från innehållet eller 30%, beroende på vilket som är längre.|+0.2 utmärkelse|
|||

Du kan sedan skicka den totala trafik till API: et.

## <a name="calling-the-reward-api-multiple-times"></a>Anropa API: et för ersättning flera gånger

Du kan också anropa utmärkelse API: et med hjälp av samma händelse-ID som skickar annan trafik poäng. När Personalizer hämtar dessa fördelar, anger den sista ersättning för händelsen genom att sammanställa dem som anges i inställningarna för Personalizer.

Aggregering inställningar:

*  **Första**: Tar den första utmärkelse poäng för händelsen och ignorerar resten.
* **Summa**: Tar samtliga utmärkelse värden som samlas in för den händelse-ID och lägger till dem tillsammans.

Alla poäng för en händelse som har tagits emot efter den **utmärkelse väntetid**, ignoreras och påverkar inte utbildning av modeller.

Genom att lägga ihop utmärkelse poäng, kan sista belöning vara högre än 1 eller lägre än -1. Detta kommer inte att tjänsten misslyckas.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Metodtips för att beräkna utmärkelse poäng

* **Överväg att SANT indikatorer för lyckade anpassnings**: Det är enkelt att tänka i termer av klick, men bra ersättning baseras på vad du vill att användarna ska *uppnå* i stället för vad du vill att användare *gör*.  Nu belönar vi på klick kan till exempel leda till att välja innehåll som är clickbait felbenägna.

* **Använd ersättning score för hur bra anpassning som fungerade**: Anpassa en film förslag förhoppningsvis leder användaren titta på filmen och ger den höga omdömet. Eftersom filmklassificering beror förmodligen på många saker (kvaliteten på agerar, stämning för användaren), det är inte en bra utmärkelse signal för hur väl *anpassningen* fungerade. Du tittar på de första minuterna i filmen, kan dock finnas en bättre signal personanpassning effektivitet och skicka ersättning 1 efter 5 minuter kommer att vara en bättre signal.

* **Belöningar gäller endast för RewardActionID**: Personalizer gäller alla för att förstå den åtgärd som angetts i RewardActionID effekt. Om du vill visa andra åtgärder och användaren klickar på på dem. ska trafik vara noll.

* **Överväg att oönskade konsekvenser**: Skapa utmärkelse funktioner som leder till ansvarig för patienterna med [etik och ansvarar användning](ethics-responsible-use.md).

* **Använd inkrementella belöningar**: Att lägga till partiella poäng för mindre användarbeteenden hjälper Personalizer att uppnå bättre belöningar. Den här inkrementella trafik kan algoritmen veta det börjar bli närmare engagerar sig användaren i sista önskat beteende.
    * Om du visar en lista över filmer, om användaren för muspekaren över den första mallen för ett tag att se mer information, kan du bestämma att vissa användarengagemanget har inträffat. Beteendet kan räkna med en poäng för ersättning av 0,1. 
    * Om användaren öppnas sidan och sedan avslutades, vara Utmärkelse poängen 0.2. 

## <a name="reward-wait-time"></a>Väntetid för ersättning

Personalizer kommer korrelera information för en rangordning anropa med frukterna skickas i utmärkelse anrop för att träna modellen. Dessa kan komma vid olika tidpunkter. Personalizer väntar under en begränsad tid när rangordnas anropet har hänt, även om rangordnas anropet har gjorts som en inaktiv händelse och aktiverat senare startar.

Om den **utmärkelse väntetid** upphör att gälla, och det har ingen information om betalning, ersättning standard tillämpas på händelsen för utbildning. Maximal väntetid varaktigheten är sex dagar.

## <a name="best-practices-for-setting-reward-wait-time"></a>Metodtips för att ställa in räkning väntetid

Följ dessa rekommendationer för bättre resultat.

* Kontrollera den utmärkelse väntetid så korta som möjligt, och lämna tillräckligt med tid för att få feedback från användare. 

<!--@Edjez - storage quota? -->

* Välj inte en varaktighet som är kortare än den tid som behövs för att få feedback. Till exempel om några av dina belöningar finns när en användare har sett 1 minut i en video, experiment bör innehålla minst dubbelt som.

## <a name="next-steps"></a>Nästa steg

* [Förstärkande inlärning](concepts-reinforcement-learning.md) 
* [Försök rangordning API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Försök utmärkelse API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
