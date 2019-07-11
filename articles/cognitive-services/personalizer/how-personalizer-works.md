---
title: Hur fungerar Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer använder maskininlärning att identifiera vilken åtgärd som ska användas i ett sammanhang. Varje learning slinga har en modell som har tränats exklusivt på data som du har skickat till den via rangordning och utmärkelse anropar. Varje learning loop är helt oberoende av varandra.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 38480d3cc32d53084b79af627e4f7e6ae7dcc03d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722368"
---
# <a name="how-personalizer-works"></a>Så här fungerar Personanpassning

Personalizer använder maskininlärning att identifiera vilken åtgärd som ska användas i ett sammanhang. Varje learning slinga har en modell som har tränats exklusivt på data som du har skickat till det via **rangordning** och **utmärkelse** anrop. Varje learning loop är helt oberoende av varandra. Skapa en learning skapas för varje del eller beteendet för ditt program som du vill anpassa.

För varje loop **anropa rangordning API med** baserat på den aktuella kontexten med:

* Listan över möjliga åtgärder: objekt från att välja övre åtgärd.
* Lista över [kontext funktioner](concepts-features.md): sammanhangsmässigt relevanta data, till exempel användare, innehåll och sammanhang.

Den **rangordning** API bestämmer sig att använda antingen:

* _Utnyttja_: Den aktuella modellen för att avgöra den bästa åtgärd baserat på senaste data.
* _Utforska_: Välj en annan åtgärd i stället för den översta åtgärden.

Den **utmärkelse** API:

* Samlar in data för att träna modellen genom att spela in funktioner och utmärkelse poäng för varje rangordnas anrop.
* Använder dessa data för att uppdatera modellen baserat på inställningarna i den _Learning princip_.

## <a name="architecture"></a>Arkitektur

Följande bild visar arkitekturen flödet för att anropa rangordning samt belöningar anrop:

![alternativ text](./media/how-personalizer-works/personalization-how-it-works.png "så här fungerar för anpassning")

1. Personalizer använder en intern AI-modell för att fastställa rangordningen för åtgärden.
1. Tjänsten bestämmer sig för att utnyttja den aktuella modellen eller utforska nya alternativ för modellen.  
1. Rangordning resultatet skickas till EventHub.
1. När Personalizer får trafik, skickas trafik till EventHub. 
1. Rangordning och utmärkelse kopplas ihop.
1. AI-modellen uppdateras baserat på resultatet korrelation.
1. Inferens-motorn har uppdaterats med den nya modellen. 

## <a name="research-behind-personalizer"></a>Utvecklades Personalizer

Personalizer baseras på banbrytande vetenskap och forskning i området [förstärkande inlärning](concepts-reinforcement-learning.md) inklusive dokument, forskning och pågående delar av utforskning i Microsoft Research.

## <a name="terminology"></a>Terminologi

* **Learning Loop**: Du kan skapa en learning skapas för varje del av ditt program som kan dra nytta av anpassning. Om du har mer än en lösning för att anpassa kan skapa en loop för varje. 

* **Åtgärder**: Åtgärder är innehållsposter, till exempel produkter eller kampanjer att välja bland. Personalizer väljer den översta åtgärden ska visas för användarna, som ibland kallas den _belöna åtgärd_, via API: et för rangordning. Varje åtgärd kan ha funktioner som skickas med rangordnas begäran.

* **Kontext**: Innehåller information om din kontext, till exempel för att ge en mer exakta rangordning:
    * Dina användare.
    * Den enhet som de är anslutna. 
    * Den aktuella tiden.
    * Andra data om den aktuella situationen.
    * Historiska data för användaren eller kontext.

    Specifika programmet kan ha olika kontextinformation. 

* **[Funktioner](concepts-features.md)** : En enhet med information om ett innehållsobjekt eller en användarkontext.

* **Utmärkelse**: Ett mått på hur användaren svarade rangordning-API: et returnerade åtgärden som en poäng mellan 0 och 1. Värdet 0 till 1 anges av din affärslogik som baseras på hur valet hjälpte uppnå affärsmålen för anpassning. 

* **Utforskning**: Tjänsten Personalizer möjligheterna när istället för att returnera den bästa åtgärden den väljer en annan åtgärd för användaren. Tjänsten Personalizer undviker drift, stagnation, och kan anpassas till Pågående användarbeteende genom att utforska. 

* **Experimentlängd**: Hur lång tid som tjänsten Personalizer väntar ersättning, med början från den tidpunkt då rangordnas anropet har inträffat för händelsen.

* **Inaktiva händelser**: En inaktiv händelse är en där du kallas rankning, men du inte vet någonsin visas resultatet, på grund av klienten programmet beslut. Inaktiva händelser kan du skapa och lagra personanpassning resultatet och sedan bestämmer dig för att ignorera dem senare utan att påverka machine learning-modell.

* **Modellen**: En modell för Personalizer samlar in alla data som har lärt dig om användarnas beteende och hämta utbildningsdata från en kombination av de argument som du skickar till rangordning samt belöningar anrop och med ett beteende för utbildning som bestäms av Learning principen. 

## <a name="example-use-cases-for-personalizer"></a>Exempel användningsfall för Personalizer

* Avsiktshantering information & tvetydigheter: hjälp användarna får en bättre upplevelse när deras avsikten är inte bort genom att tillhandahålla ett alternativ som är anpassad för varje användare.
* Förslag på menyer och alternativ som standard: har Roboten föreslår det mest sannolika objektet på ett personligt sätt som ett första steg i stället för att presentera en impersonal menyn eller en lista med alternativ.
* Bot egenskaper & ton: Överväg att varierande dessa egenskaper i en anpassad sätt för robotar som kan variera toning, utförlighetsnivå och typ av text.
* Meddelande- & avisering innehåll: Bestäm vilken text som ska användas för aviseringar för att engagera användare mer.
* Meddelanden och aviseringar tidpunkt: få personlig inlärning av när du ska skicka meddelanden till användare att engagera dem mer.

## <a name="checklist-for-applying-personalizer"></a>Checklista för att tillämpa Personalizer

Du kan använda Personalizer i situationer där:

* Du har en företags- eller användbarhet målet för ditt program.
* Du har en plats i ditt program där beslutet sammanhangsberoende av vad som ska visas för användarna att förbättra målet.
* Det bästa valet kan och ska hämtas från kollektiva användaren beteende och Totalt antal utmärkelse poäng.
* Användningen av maskininlärning för anpassning följer [ansvarig Använd riktlinjer](ethics-responsible-use.md) och alternativ för ditt team.
* Beslutet kan uttryckas som rangordning det bästa alternativet ([åtgärd](concepts-features.md#actions-represent-a-list-of-options) från en begränsad uppsättning alternativ.
* Hur bra val arbetat kan beräknas genom att din affärslogik genom att mäta någon aspekt av användarnas beteende och anges i ett tal mellan 1 och 1.
* Poängen trafik ta med inte i för många confounding eller externa faktorer, särskilt experimentlängd är låg trafik poängen kan beräknas när den är fortfarande relevanta.
* Du kan uttrycka kontext för rangordningen som en ordlista med minst 5 funktioner som du tror att kunna fatta rätt beslut och som inte innehåller personligt identifierbar information.
* Du har information om varje åtgärd som en ordlista med minst 5 attribut eller funktioner som du tror kan Personalizer fatta rätt beslut.
* Du kan behålla data för långvariga tillräckligt för att lagra en historik över minst 100 000 interaktioner.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Machine learning-överväganden för att tillämpa Personalizer

Personalizer baseras på förstärkt learning, en metod till machine learning som hämtar undervisats av feedback du ge den. 

Personalizer lära dig bäst i situationer där:
* Det finns tillräckligt med händelser för att hålla koll på optimal personanpassning om problemet drifts över tid (t.ex inställningar i nyheter eller Mode). Personalizer anpassas automatiskt löpande ändringar i verkligheten, men resultatet inte inte bra om det inte finns tillräckligt med händelser och data kan lära sig från att identifiera och reglera på nya mönster. Du bör välja ett användningsfall som sker tillräckligt ofta. Överväg att söker användningsfall som sker på minst 500 gånger per dag.
* Kontext och åtgärder som har tillräckligt med funktioner för att förenkla inlärningen.
* Det finns mindre än 50 åtgärder för rangordning per anrop.
* Dina inställningar för kvarhållning av data att Personalizer att samla in tillräckligt med data för att utföra offline utvärderingar och optimering av Grupprincip. Detta är vanligtvis minst 50 000 datapunkter.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Hur du använder Personalizer i ett webbprogram

Lägga till en loop i ett webbprogram omfattar:

* Avgör vilken lösning för att anpassa, vilka åtgärder och funktioner du har, vilken kontext vilka funktioner du vill använda, samt vilken trafik som du kan ange.
* Lägg till en referens till anpassning SDK i ditt program.
* Anropa rangordning API när du är redo att anpassa.
* Store den händelse-ID. Du skickar ersättning med utmärkelse API senare.
1. Anropa aktivera för händelsen när du är säker på att användaren har sett din anpassade sida.
1. Vänta på användarens val av rankad innehåll. 
1. Anropa utmärkelse API för att ange hur väl utdata från API: et rangordning gjorde.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Hur du använder Personalizer med en chattrobot

I det här exemplet visas hur du använder anpassning till förslag standard i stället för att användaren en serie menyer eller val varje gång.

* Hämta den [kod](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) för det här exemplet.
* Konfigurera din bot-lösning. Se till att publicera dina LUIS-program. 
* Hantera rangordning och utmärkelse API-anrop för robot.
    * Lägg till kod för att hantera LUIS avsikt bearbetning. Om den **ingen** returneras som främsta syftet eller främsta syftet poäng ligger under tröskeln för ditt företag logic kan skicka avsikter lista till Personalizer till rangordning avsikter.
    * Visa avsikt lista användaren som valbara länkar med en första avsikt som top-Rank avsikten från rangordning API-svar.
    * Samla in användarens val och skicka detta i utmärkelse API-anrop. 

### <a name="recommended-bot-patterns"></a>Rekommenderade bot-mönster

* Göra Personalizer rangordning API-anrop varje gång en tvetydigheter krävs, till skillnad från cachelagring av resultaten för varje användare. Resultatet av disambiguating avsikt kan ändras över tid för en person och så att rangordning-API för att utforska avvikelser att påskyndas övergripande learning.
* Välj en interaktion är vanligt med många användare så att du har tillräckligt med data för att anpassa. Grundläggande frågor kan till exempel vara bättre anpassas än mindre förtydliganden djupt i konversationen diagrammet som endast några användare får åtkomst till.
* Använd rangordning API-anrop för att aktivera ”första förslaget är rätt” konversationer, där användaren hämtar och ”vill du X”? eller ”Menade du X”? och användaren kan bara bekräfta; till skillnad från ger alternativ för användaren där måste de välja från en meny. Till exempel användare: ”Jag skulle vilja sortera en kaffe” Bot: ”vill du göra en dubbla espresso”?. Det här sättet utmärkelse signalen är också starkt som används direkt till det enda förslaget.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Hur du använder Personalizer med en rekommenderad lösning

Använda din rekommendationsmotor för att filtrera ner en stor katalog till några objekt som kan sedan presenteras som 30 möjliga åtgärder som skickas till API: et för rangordning.

Du kan använda rekommendationsmotorer Personalizer:

* Konfigurera den [rekommenderad lösning](https://github.com/Microsoft/Recommenders/). 
* När du visar en sida, anropa rekommendation-modellen för att få en kort lista med rekommendationer.
* Anropa anpassning för att rangordna utdata från rekommenderad lösning.
* Skicka feedback om din användaråtgärd med utmärkelse API-anrop.


## <a name="pitfalls-to-avoid"></a>Man vill undvika

* Använd inte Personalizer där anpassade beteendet inte något som kan identifieras i alla användare men i stället något som ska registreras för specifika användare, eller kommer från en viss användare lista med alternativ. Till exempel med hjälp av Personalizer för att föreslå en första pizza ordning från en lista över 20 möjliga menyalternativ är användbart, men vilken kontakt för att anropa från användarnas kontaktlista när kräver hjälp med barnomsorg (till exempel ”mormor”) inte är något som är anpassningsbara över användarbasen.


## <a name="adding-content-safeguards-to-your-application"></a>Att lägga till innehåll skydd i ditt program

Om programmet medger för stora avvikelser i innehåll som visas för användare och några av att innehåll kan vara osäkra eller olämpliga för vissa användare, du bör planera framåt och se till att är rätt skydd att hindra användarna från att se oacceptabla innehåll. Det bästa mönstret som ska implementera skydd är: Det bästa mönstret som ska implementera skydd är:
    * Hämta listan över åtgärder för att rangordning.
    * Filtrera bort de som inte är användbara för målgruppen.
    * Endast ranka åtgärderna lönsamma.
    * Visa upp rangordnas åtgärden för användaren.

I vissa arkitekturer vara ovan sekvensen svåra att implementera. I så fall kan det finns en annan metod för att implementera skydd efter rankning, men en etablera måste göras så att åtgärder som faller utanför säkerhetsmekanismen inte används för att träna modellen Personalizer.

* Hämta listan över åtgärder för att rangordning returneras med learning inaktiveras.
* Rangordnas åtgärder.
* Kontrollera om den översta åtgärden är genomförbart.
    * Om åtgärden som övre är genomförbart, aktivera utbildning för den här rangordning och sedan visa för användaren.
    * Om den översta åtgärden inte är genomförbart, inte aktivera utbildning för den här rangordning och bestäm via logik eller alternativa metoder för vad du vill visa för användaren. Även om du använder alternativet näst bästa rankad aktiveras inte inlärning för den här rangordning.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Verifiera tillräcklig effektiviteten i Personalizer

Du kan övervaka effektiviteten i Personalizer regelbundet genom att utföra [offline utvärderingar](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Nästa steg

Förstå [där du kan använda Personalizer](where-can-you-use-personalizer.md).