---
title: Azure Functions tillförlitlig händelsebearbetning
description: Undvik saknade eventnavmeddelanden i Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561875"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions tillförlitlig händelsebearbetning

Händelsebearbetning är ett av de vanligaste scenarierna som är associerade med serverlös arkitektur. I den här artikeln beskrivs hur du skapar en tillförlitlig meddelandebehandlare med Azure Functions för att undvika att förlora meddelanden.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Utmaningar för händelseströmmar i distribuerade system

Överväg ett system som skickar händelser med en konstant hastighet av 100 händelser per sekund. Med den här takten kan flera parallella funktionsinstanser inom några minuter förbruka inkommande 100 händelser varje sekund.

Något av följande mindre optimala förhållanden är dock möjliga:

- Vad händer om händelseutgivaren skickar en skadad händelse?
- Vad händer om din Functions-instans stöter på ohanterade undantag?
- Vad händer om ett nedströmssystem kopplas från?

Hur hanterar du dessa situationer samtidigt som du bevarar programmets dataflöde?

Med köer kommer tillförlitliga meddelanden naturligt. När funktionen paras ihop med en Functions-utlösare skapas ett lås i kömeddelandet. Om bearbetningen misslyckas frigörs låset så att en annan instans kan bearbetas igen. Bearbetningen fortsätter sedan tills meddelandet utvärderas eller läggs till i en giftkö.

Även om ett enskilt kömeddelande kan finnas kvar i en återförsökscykel fortsätter andra parallella körningar att fortsätta att dequeueing återstående meddelanden. Resultatet är att det totala genomströmflödet i stort sett inte påverkas av ett dåligt meddelande. Lagringsköer garanterar dock inte beställning och är inte optimerade för de höga dataflödeskrav som krävs av Event Hubs.

Azure Event Hubs innehåller däremot inget låskoncept. Event Hubs-händelser fungerar mer som en videospelare för att möjliggöra funktioner som högdataflöde, flera konsumentgrupper och återuppspelningsförmåga. Händelser läss från en enda punkt i strömmen per partition. Från pekaren kan du läsa framåt eller bakåt från den platsen, men du måste välja att flytta pekaren för händelser att bearbeta.

När fel uppstår i en ström, om du bestämmer dig för att behålla pekaren på samma plats, blockeras händelsebearbetningen tills pekaren är avancerad. Med andra ord, om pekaren stoppas för att hantera problem med att bearbeta en enskild händelse, börjar de obearbetade händelserna samlas.

Azure Functions undviker dödlägen genom att flytta strömmens pekare oavsett framgång eller misslyckande. Eftersom pekaren fortsätter att avancera måste dina funktioner hantera fel på rätt sätt.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Så här använder Azure Functions eventhubbarhändelser

Azure Functions använder Event Hub-händelser medan du cyklar genom följande steg:

1. En pekare skapas och sparas i Azure Storage för varje partition i händelsehubben.
2. När nya meddelanden tas emot (i en batch som standard) försöker värden utlösa funktionen med batchen med meddelanden.
3. Om funktionen slutför körningen (med eller utan undantag) går pekaren framåt och en kontrollpunkt sparas i lagringskontot.
4. Om villkoren hindrar funktionskörningen från att slutföras, misslyckas värden att föra pekaren framåt. Om pekaren inte är avancerad, sedan senare kontroller sluta bearbeta samma meddelanden.
5. Upprepa steg 2–4

Detta visar några viktiga punkter:

- *Ohanterade undantag kan leda till att du förlorar meddelanden.* Körningar som resulterar i ett undantag fortsätter att föra pekaren framåt.
- *Funktioner garanterar minst en gång leverans.* Din kod och beroende system kan behöva ta hänsyn till [det faktum att samma meddelande kan tas emot två gånger](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Hantering av undantag

Som en allmän regel bör varje funktion innehålla ett [try/catch-block](./functions-bindings-error-pages.md) på högsta kodnivå. Närmare bestämt bör alla funktioner som använder `catch` Event Hubs-händelser ha ett block. På så sätt hanterar catch-blocket felet innan pekaren fortskrider när ett undantag utlöses.

### <a name="retry-mechanisms-and-policies"></a>Återförsöksmekanismer och principer

Vissa undantag är övergående till sin natur och visas inte igen när en åtgärd försöker igen ögonblick senare. Det är därför det första steget är alltid att försöka igen åtgärden. Du kan skriva nytt försök bearbetningsregler själv, men de är så vanligt att ett antal verktyg finns tillgängliga. Med hjälp av dessa bibliotek kan du definiera robusta principer för återförsök, vilket också kan bidra till att bevara bearbetningsordningen.

Genom att införa felhanteringsbibliotek i dina funktioner kan du definiera både grundläggande och avancerade principer för återförsök. Du kan till exempel implementera en princip som följer ett arbetsflöde som illustreras av följande regler:

- Försök att infoga ett meddelande tre gånger (eventuellt med en fördröjning mellan försöken).
- Om det slutliga resultatet av alla försök är ett fel lägger du till ett meddelande i en kö så att bearbetningen kan fortsätta i dataströmmen.
- Skadade eller obearbetade meddelanden hanteras senare.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) är ett exempel på ett återhämtnings- och övergående felhanteringsbibliotek för C#-program.

När du arbetar med förordnade C#-klassbibliotek kan du med [undantagsfilter](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) köra kod när ett ohanterat undantag inträffar.

Exempel som visar hur du använder undantagsfilter är tillgängliga i [Azure WebJobs SDK-repo.](https://github.com/Azure/azure-webjobs-sdk/wiki)

## <a name="non-exception-errors"></a>Icke-undantagsfel

Vissa problem uppstår även när ett fel inte är närvarande. Tänk dig till exempel ett fel som inträffar mitt i en körning. I det här fallet, om en funktion inte slutför körningen, förskjutningen pekaren aldrig progressed. Om pekaren inte avancerar fortsätter alla instanser som körs efter en misslyckad körning att läsa samma meddelanden. Denna situation ger en "minst en gång"-garanti.

Försäkran om att varje meddelande bearbetas minst en gång innebär att vissa meddelanden kan bearbetas mer än en gång. Din funktion apps måste vara medveten om denna möjlighet och måste byggas kring [principerna för idempotency](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Stoppa och starta om körningen

Även om några fel kan vara acceptabla, vad händer om din app har allvarliga fel? Du kanske vill sluta utlösa händelser tills systemet når ett felfritt tillstånd. Att ha möjlighet att pausa bearbetning uppnås ofta med ett kretsbrytare mönster. Kretsbrytarmönstret gör att appen kan "bryta kretsen" av händelseprocessen och återupptas vid ett senare tillfälle.

Det finns två delar som krävs för att genomföra en kretsbrytare i en händelseprocess:

- Delat tillstånd i alla instanser för att spåra och övervaka kretsens hälsa
- Huvudprocess som kan hantera kretstillståndet (öppet eller stängt)

Implementeringsinformationen kan variera, men för att dela tillstånd mellan instanser behöver du en lagringsmekanism. Du kan välja att lagra tillstånd i Azure Storage, en Redis-cache eller något annat konto som är tillgängligt för en samling funktioner.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) eller [varaktiga entiteter](./durable/durable-functions-overview.md) är en naturlig passform för att hantera arbetsflödet och kretstillståndet. Andra tjänster kan fungera lika bra, men logikappar används för det här exemplet. Med hjälp av logikappar kan du pausa och starta om en funktions körning, vilket ger dig den kontroll som krävs för att implementera kretsbrytarmönstret.

### <a name="define-a-failure-threshold-across-instances"></a>Definiera ett tröskelvärde för fel över instanser

För att ta hänsyn till flera instanser bearbetning händelser samtidigt, beständiga delade externa tillstånd behövs för att övervaka hälsan för kretsen.

En regel som du kan välja att implementera kan tvinga fram följande:

- Om det finns fler än 100 eventuella fel inom 30 sekunder i alla instanser bryter du kretsen och slutar utlösa nya meddelanden.

Implementeringsinformationen varierar med tanke på dina behov, men i allmänhet kan du skapa ett system som:

1. Logga fel på ett lagringskonto (Azure Storage, Redis, etc.)
1. När ett nytt fel loggas bör du kontrollera rullande antal för att se om tröskelvärdet är uppfyllt (till exempel mer än 100 under de senaste 30 sekunderna).
1. Om tröskelvärdet uppfylls avger du en händelse till Azure Event Grid som talar om för systemet att bryta kretsen.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Hantera kretstillstånd med Azure Logic Apps

Följande beskrivning markerar ett sätt att skapa en Azure Logic App för att stoppa en functions-app från bearbetning.

Azure Logic Apps levereras med inbyggda kopplingar till olika tjänster, funktioner tillståndskänsliga orkestreringar och är ett naturligt val för att hantera kretstillstånd. När du har upptäckt kretsen måste brytas kan du skapa en logikapp för att implementera följande arbetsflöde:

1. Utlösa ett event grid-arbetsflöde och stoppa Azure-funktionen (med Azure Resource-anslutningen)
1. Skicka ett e-postmeddelande som innehåller ett alternativ för att starta om arbetsflödet

E-postmottagaren kan undersöka kretsens hälsa och, när så är lämpligt, starta om kretsen via en länk i e-postmeddelandet. När arbetsflödet startar om funktionen bearbetas meddelanden från den senaste händelsehubbkontrollpunkten.

Med den här metoden går inga meddelanden förlorade, alla meddelanden bearbetas i ordning och du kan bryta kretsen så länge det behövs.

## <a name="resources"></a>Resurser

- [Tillförlitliga prover för händelsebearbetning](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure varaktiga funktioner brytare](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Felhantering av Azure Functions](./functions-bindings-error-pages.md)
- [Automatisera storleksändring av överförda bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Skapa en funktion som kan integreras med Azure Logic Apps](./functions-twitter-email.md)
