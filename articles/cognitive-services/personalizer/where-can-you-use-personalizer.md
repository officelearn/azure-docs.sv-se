---
title: Var och hur man använder - Personalizer
description: Personalizer kan tillämpas i alla situationer där ditt program kan välja rätt objekt, åtgärd eller produkt att visa - för att göra upplevelsen bättre, uppnå bättre affärsresultat eller förbättra produktiviteten.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: c562d7a1853736204a7a03262547e083bd85fb75
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617671"
---
# <a name="where-and-how-to-use-personalizer"></a>Var och hur man använder Personalizer

Använd Personalizer i alla situationer där ditt program måste välja rätt åtgärd (innehåll) att visa - för att göra upplevelsen bättre, uppnå bättre affärsresultat eller förbättra produktiviteten.

Personalizer använder förstärkningsinlärning för att välja vilken åtgärd (innehåll) som ska visas för användaren. Urvalet kan variera drastiskt beroende på kvantitet, kvalitet och distribution av data som skickas till tjänsten.

## <a name="example-use-cases-for-personalizer"></a>Exempel på användningsfall för Personalizer

* **Avsikt förtydligande & disambiguation:** hjälp dina användare att få en bättre upplevelse när deras avsikt är inte klart genom att tillhandahålla ett alternativ som är personlig.
* **Standardförslag** för menyer & alternativ: har bot föreslå det mest sannolika objektet på ett personligt sätt som ett första steg, istället för att presentera en opersonlig meny eller lista med alternativ.
* **Bot egenskaper & tonen:** för robotar som kan variera ton, verbositet och skrivstil, överväga att variera dessa egenskaper.
* **Meddelande & aviseringsinnehåll**: bestäm vilken text som ska användas för aviseringar för att engagera användarna mer.
* **Meddelande & aviseringstid**: har personlig inlärning av när du ska skicka meddelanden till användare för att engagera dem mer.


## <a name="expectations-required-to-use-personalizer"></a>Förväntningar som krävs för att använda Personalizer

Du kan använda Personalizer i situationer där du träffas eller kan implementera följande riktlinjer.

|Riktlinjer|Förklaring|
|--|--|
|Affärsmål|Du har ett affärs- eller användbarhetsmål för din applikation.|
|Innehåll|Du har en plats i ditt program där ett kontextuellt beslut om vad som ska visas för användarna kommer att förbättra det målet.|
|Innehållskvantitet|Du har färre än 50 åtgärder att rangordna per samtal.|
|Aggregerade data|Det bästa valet kan och bör läras av kollektivt användarbeteende och total belöning poäng.|
|Etisk användning|Användningen av maskininlärning för anpassning följer [riktlinjer för ansvarsfull användning](ethics-responsible-use.md) och val som du har valt.
|Bästa singelalternativet|Det kontextuella beslutet kan uttryckas som rangordning det bästa alternativet (åtgärd) från en begränsad uppsättning val.|
|Poängsatt resultat|Hur väl det rankade valet fungerade för din ansökan kan bestämmas genom att mäta någon aspekt av användarbeteende, och uttrycka det i en _[belöning poäng](concept-rewards.md)_.|
|Relevant tidpunkt|Belöningen poäng inte ta in alltför många förvirrande eller externa faktorer. Experimentets varaktighet är tillräckligt låg för att belöningspoängen kan beräknas medan den fortfarande är relevant.|
|Tillräckliga sammanhangsfunktioner|Du kan uttrycka kontexten för rangordningen som en lista över minst 5 [funktioner](concepts-features.md) som du tror skulle bidra till att göra rätt val, och som inte innehåller användarspecifik identifierbar information.|
|Tillräckliga åtgärdsfunktioner|Du har information om varje innehåll val, _åtgärd_, som en lista över minst 5 [funktioner](concepts-features.md) som du tror kommer att hjälpa Personalizer göra rätt val.|
|Dagliga data|Det finns tillräckligt med händelser för att hålla koll på optimal personalisering om problemet driver över tiden (t.ex. preferenser i nyheter eller mode). Personalizer kommer att anpassa sig till kontinuerlig förändring i den verkliga världen, men resultaten kommer inte att vara optimala om det inte finns tillräckligt med händelser och data för att lära sig av att upptäcka och bosätta sig på nya mönster. Du bör välja ett användningsfall som händer tillräckligt ofta. Överväg att leta efter användningsfall som inträffar minst 500 gånger per dag.|
|Historiska data|Ditt program kan behålla data tillräckligt länge för att ackumulera en historik på minst 100 000 interaktioner. Detta gör att Personalizer kan samla in tillräckligt med data för att utföra offlineutvärderingar och principoptimering.|

**Använd inte Personalizer** där det personliga beteendet inte är något som kan upptäckas för alla användare. Till exempel, använda Personalizer att föreslå en första pizza ordning från en lista med 20 möjliga menyalternativ är användbart, men vilken kontakt att ringa från användarnas kontaktlista när du behöver hjälp med barnomsorg (till exempel "Mormor") är inte något som är personalizable över hela din användarbas.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Så här använder du Personalizer i ett webbprogram

Lägga till en utbildningsloop i ett webbprogram innehåller:

* Bestäm vilken upplevelse du ska anpassa, vilka åtgärder och funktioner du har, vilka sammanhangsfunktioner du ska använda och vilken belöning du ska ange.
* Lägg till en referens till AnpassningS-SDK i ditt program.
* Anropa Rank API när du är redo att anpassa.
* Lagra eventId. Du skickar en belöning med belönings-API:et senare.
1. Ring Aktivera för händelsen när du är säker på att användaren har sett din personliga sida.
1. Vänta på användarval av rangordnat innehåll.
1. Anropa belönings-API för att ange hur väl utdata för Rank API gjorde.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Hur man använder Personalizer med en chatt bot

I det här exemplet får du se hur du använder Anpassning för att göra ett standardförslag i stället för att skicka användaren ner en serie menyer eller alternativ varje gång.

* Hämta [koden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) för det här exemplet.
* Ställ in din botlösning. Se till att publicera luis-programmet.
* Hantera Api-samtal för rank och belöning för bot.
    * Lägg till kod för att hantera LUIS-avsiktsbearbetning. Om **ingen** returneras som den högsta avsikten eller den högsta avsiktens poäng är lägre än tröskelvärdet för affärslogik skickar du avsiktslistan till Personalizer för att rangordna avsikter.
    * Visa avsiktslista för användaren som valbara länkar med den första avsikten är den högst rankade avsikten från Rank API-svar.
    * Fånga användarens val och skicka detta i belöning API-anropet.

### <a name="recommended-bot-patterns"></a>Rekommenderade botmönster

* Gör Personalizer Rank API-anrop varje gång en disambiguation behövs, i motsats till cachelagringsresultat för varje användare. Resultatet av disambiguating avsikt kan ändras med tiden för en person, och gör det möjligt för Rank API att utforska avvikelser kommer att påskynda övergripande lärande.
* Välj en interaktion som är gemensam med många användare så att du har tillräckligt med data för att anpassa. Till exempel kan inledande frågor vara bättre passar än mindre förtydliganden djupt i konversationsdiagrammet som endast ett fåtal användare kan komma till.
* Använd Rank API-anrop för att aktivera "första förslag är rätt" konversationer, där användaren får frågan "Vill du X?" eller "Menade du X?" och användaren kan bara bekräfta; i motsats till att ge alternativ till användaren där de måste välja från en meny. Till exempel, Användare:"Jag skulle vilja beställa en kaffe" Bot: "Vill du ha en dubbel espresso?". På så sätt är belöningen signalera också stark, som den anför direkt till ett förslag.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Så här använder du Personalizer med en rekommendationslösning

Många företag använder rekommendationsmotorer, marknadsförings- och kampanjverktyg, målgruppssegmentering och kluster, samarbetsfiltrering och andra sätt att rekommendera produkter från en stor katalog till kunder.

[Microsoft Recommenders GitHub-databasen](https://github.com/Microsoft/Recommenders) innehåller exempel och metodtips för system med byggrekommendationer, förutsatt att de är jupyter-anteckningsböcker. Det ger fungerande exempel för att förbereda data, bygga modeller, utvärdera, trimma och operationalisera rekommendationsmotorerna, för många vanliga metoder, inklusive xDeepFM, SAR, ALS, RBM, DKN.

Personalizer kan arbeta med en rekommendation motor när den är närvarande.

* Rekommendationsmotorer tar stora mängder artiklar (till exempel 500 000) och rekommenderar en delmängd (till exempel de 20 bästa) från hundratals eller tusentals alternativ.
* Personalizer tar ett litet antal åtgärder med massor av information om dem och rangordnar dem i realtid för ett visst rikt sammanhang, medan de flesta rekommendationsmotorer bara använder ett fåtal attribut om användare, produkter och deras interaktioner.
* Personalizer är utformad för att självständigt utforska användarpreferenser hela tiden, vilket ger bättre resultat där innehållet förändras snabbt, till exempel nyheter, live-evenemang, live community-innehåll, innehåll med dagliga uppdateringar eller säsongsinnehåll.

En vanlig användning är att ta utgången av en rekommendationsmotor (till exempel de 20 bästa produkterna för en viss kund) och använda den som indataåtgärder för Personalizer.

## <a name="adding-content-safeguards-to-your-application"></a>Lägga till innehållsskydd i din applikation

Om ditt program tillåter stora avvikelser i innehåll som visas för användarna, och en del av innehållet kan vara osäkert eller olämpligt för vissa användare, bör du planera i förväg för att se till att rätt skyddsåtgärder finns på plats för att förhindra att användarna ser oacceptabelt innehåll. Det bästa mönstret för att genomföra skyddsåtgärder är:
    * Hämta listan över åtgärder att rangordna.
    * Filtrera bort de som inte är genomförbara för publiken.
    * Rangordna bara dessa genomförbara åtgärder.
    * Visa den högst rankade åtgärden för användaren.

I vissa arkitekturer kan ovanstående sekvens vara svår att implementera. I så fall finns det ett alternativt tillvägagångssätt för att genomföra skyddsåtgärder efter rangordningen, men en bestämmelse måste vidtas så att åtgärder som faller utanför skyddet inte används för att utbilda Personalizer-modellen.

* Hämta listan över åtgärder att rangordna, med inlärning inaktiverad.
* Rangordna åtgärder.
* Kontrollera om den översta åtgärden är genomförbar.
    * Om den översta åtgärden är genomförbar, aktivera inlärning för den här rankningen och visa den för användaren.
    * Om den översta åtgärden inte är genomförbar, inte aktivera lärande för denna rangordning, och bestämma genom din egen logik eller alternativa metoder vad som ska visas för användaren. Även om du använder det näst bäst rankade alternativet ska du inte aktivera inlärning för den här rankningen.


## <a name="next-steps"></a>Nästa steg

[Etik & ansvarsfull användning](ethics-responsible-use.md).
