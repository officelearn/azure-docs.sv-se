---
title: Hantera begränsningsproblem, eller "429 - För många begäranden" fel
description: Så här kan du kringgå begränsningsproblem eller HTTP 429 För många begärandens fel i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272686"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Hantera begränsningsproblem (429 - "För många begäranden" fel) i Azure Logic Apps

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md)returnerar logikappen felet ["HTTP 429 För många begäranden"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) när det uppstår begränsning, vilket inträffar när antalet begäranden överskrider den hastighet med vilken målet kan hantera under en viss tidsperiod. Begränsning kan skapa problem som fördröjd databearbetning, minskad prestandahastighet och fel som att överskrida den angivna återförsöksprincipen.

![Begränsning i SQL Server-anslutning](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Här är några vanliga typer av begränsning som logikappen kan uppleva:

* [Logikapp](#logic-app-throttling)
* [Kontakt](#connector-throttling)
* [Destinationsservice eller destinationssystem](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Begränsning av logikapp

Azure Logic Apps-tjänsten har sina egna [begränsningar för dataflöde](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Så om logikappen överskrider dessa gränser begränsas logikappresursen, inte bara en viss instans eller kör.

Om du vill hitta begränsningshändelser på den här nivån kontrollerar du logikappens **mätfönster** i Azure-portalen.

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Välj **Mått**under **Övervakning**på logikapp-menyn .

1. Under **Diagramrubrik**väljer du **Lägg till mått** så att du lägger till ett annat mått i det befintliga.

1. Välj **Åtgärd begränsade händelser**i den första måttfältet i listan **METRIC** . Välj **Utlösar begränsar händelser**i den andra måttfältet i listan **METRIC** .

Om du vill hantera begränsning på den här nivån har du följande alternativ:

* Begränsa antalet logikappinstanser som kan köras samtidigt.

  Om logikappens utlösande tillstånd uppfylls mer än en gång samtidigt körs som standard flera utlösarinstanser för logikappen samtidigt eller *parallellt*. Det här beteendet innebär att varje utlösarinstans utlöses innan föregående arbetsflödesinstans körs.

  Även om standardantalet utlösarinstanser som samtidigt kan köras är [obegränsat,](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)kan du begränsa det här antalet genom [att aktivera utlösarens samtidighetsinställning](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)och vid behov välja en annan gräns än standardvärdet.

* Aktivera läget med högt dataflöde.

  En logikapp har en [standardgräns för antalet åtgärder som kan köras över ett rullande intervall på 5 minuter](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Om du vill höja den här gränsen till det maximala antalet åtgärder aktiverar du [läget för högt dataflöde](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) i logikappen.

* Inaktivera matrisdebattering ("dela på") i utlösare.

  Om en utlösare returnerar en matris för de återstående arbetsflödesåtgärderna som ska bearbetas, delar utlösarens [ **Split On-inställning** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) upp matrisobjekten och startar en arbetsflödesinstans för varje matrisobjekt, vilket effektivt utlöser flera samtidiga körningar upp till gränsen [ **Split On** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Om du vill styra begränsningen inaktiverar du **beteendet Dela på** och har en logikapp som bearbetar hela matrisen med ett enda anrop i stället för att hantera ett enskilt objekt per anrop.

* Refactor åtgärder i mindre logik apps.

  Som tidigare nämnts är en logikapp begränsad till ett [standardantal åtgärder som kan köras under en 5-minutersperiod](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Även om du kan öka den här gränsen genom att aktivera [högt dataflödesläge](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)kan du också överväga om du vill dela upp logikappens åtgärder i mindre logikappar så att antalet åtgärder som körs i varje logikapp förblir under gränsen. På så sätt minskar du belastningen på en enda logikappresurs och distribuerar belastningen över flera logikappar. Den här lösningen fungerar bättre för åtgärder som hanterar stora datauppsättningar eller snurrar upp så många samtidiga åtgärder som körs samtidigt, loopiterationer eller åtgärder inuti varje loopiteration som de överskrider gränsen för körning av åtgärder.

  Den här logikappen gör till exempel allt arbete för att hämta tabeller från en SQL Server-databas och hämtar raderna från varje tabell. **För varje** slinga iterar den samtidigt genom varje tabell så att åtgärden Hämta **rader returnerar** raderna för varje tabell. Baserat på mängden data i dessa tabeller kan dessa åtgärder överskrida gränsen för åtgärdskörningar.

  ![Logikapp "före" refactoring](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Efter refactoring är logikappen nu en överordnad och underordnad logikapp. Den överordnade hämtar tabellerna från SQL Server och anropar sedan en underordnad logikapp för varje tabell för att hämta raderna:

  ![Skapa logikapp för en åtgärd](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Här är den underordnade logikappen som anropas av den överordnade logikappen för att hämta raderna för varje tabell:

  ![Skapa en annan logikapp för en andra åtgärd](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Strypning av koppling

Varje koppling har sina egna begränsningsgränser, som du hittar på kopplingens tekniska referenssida. Azure Service [Bus-anslutningsappen](https://docs.microsoft.com/connectors/servicebus/) har till exempel en begränsningsgräns som tillåter upp till 6 000 anrop per minut, medan SQL Server-anslutningen har [begränsningsgränser som varierar beroende på åtgärdstypen](https://docs.microsoft.com/connectors/sql/).

Vissa utlösare och åtgärder, till exempel HTTP, har en ["återförsöksprincip"](../logic-apps/logic-apps-exception-handling.md#retry-policies) som du kan anpassa baserat på [principbegränsningarna](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) för återförsök för att implementera undantagshantering. Den här principen anger om och hur ofta en utlösare eller åtgärd försöker en begäran när den ursprungliga begäran misslyckas eller time out och resulterar i ett 408, 429 eller 5xx-svar. Så när begränsningen startar och returnerar ett 429-fel följer Logic Apps principen för återförsök där stöds.

Om du vill veta om en utlösare eller åtgärd stöder principer för återförsök kontrollerar du inställningarna för utlösaren eller åtgärden. Om du vill visa en utlösare eller åtgärds försök att försöka igen går du till logikappens körningarhistorik, väljer den körning som du vill granska och expanderar utlösaren eller åtgärden för att visa information om indata, utdata och eventuella återförsök, till exempel:

![Visa åtgärdens körningshistorik, återförsök, indata och utdata](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Även om återförsökshistoriken innehåller felinformation kan det vara svårt att skilja mellan anslutningsbegränsning och [målbegränsning](#destination-throttling). I det här fallet kan du behöva granska svarets information eller utföra vissa begränsningsintervallberäkningar för att identifiera källan.

För logikappar i den globala Azure Logic Apps-tjänsten med flera innehavare sker begränsning på *anslutningsnivå.* Så, till exempel, för logikappar som körs i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), strypning händer fortfarande för icke-ISE-anslutningar eftersom de körs i den globala tjänsten Logic Apps med flera innehavare. Ise-anslutningar, som skapas av ISE-kopplingar, begränsas dock inte eftersom de körs i DIN ISE.

Om du vill hantera begränsning på den här nivån har du följande alternativ:

* Konfigurera flera anslutningar för en enskild åtgärd så att logikappen partitionerar data för bearbetning.

  Det här alternativet bör du överväga om du kan distribuera arbetsbelastningen genom att dela en åtgärds begäranden mellan flera anslutningar till samma mål med samma autentiseringsuppgifter.

  Anta till exempel att logikappen hämtar tabeller från en SQL Server-databas och sedan hämtar raderna från varje tabell. Baserat på antalet rader som du måste bearbeta kan du använda flera anslutningar och flera **för varje** loopar för att dela upp det totala antalet rader i mindre uppsättningar för bearbetning. Det här scenariot använder två **För varje** slingor för att dela det totala antalet rader på mitten. Den första **för varje** slinga använder ett uttryck som får den första halvan. Den andra **för varje** slinga använder ett annat uttryck som får den andra halvan, till exempel:<p>

    * Uttryck 1: `take()` Funktionen får framsidan av en samling. Mer information finns [ **`take()`** ](workflow-definition-language-functions-reference.md#take)i funktionen .

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Uttryck 2: `skip()` Funktionen tar bort framsidan av en samling och returnerar alla andra objekt. Mer information finns [ **`skip()`** ](workflow-definition-language-functions-reference.md#skip)i funktionen .

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Här är ett visuellt exempel som visar hur du kan använda dessa uttryck:

    ![Skapa och använda flera anslutningar för en enskild åtgärd](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Ställ in en annan anslutning för varje åtgärd.

  Det här alternativet bör du överväga om du kan distribuera arbetsbelastningen genom att sprida varje åtgärds begäranden över sin egen anslutning, även när åtgärder ansluter till samma tjänst eller system och använder samma autentiseringsuppgifter.

  Anta till exempel att logikappen hämtar tabellerna från en SQL Server-databas och hämtar varje rad i varje tabell. Du kan använda separata anslutningar så att tabellerna använder en anslutning, medan den nya varje rad använder en annan anslutning.

  ![Skapa och använda olika anslutningar för varje åtgärd](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Ändra samtidigheten eller parallellismen på en ["För varje" slinga](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Som standard körs "För varje" loopiterationer samtidigt upp till [samtidighetsgränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du har en koppling som får strypt inuti en "För varje" slinga kan du minska antalet loopiterationer som körs parallellt. Mer information finns i de här ämnena:
  
  * ["För varje" loopar - ändra samtidighet eller kör sekventiellt](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Språkschema för arbetsflödesdefinition - För varje loopar](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Språkschema för arbetsflödesdefinition - Ändra "För varje" loop samtidighet](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Språkschema för arbetsflödesdefinition – Kör "För varje" loopar sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Målservice eller systembegränsning

En koppling har sina egna begränsningsgränser, men måltjänsten eller målsystemet som anropas av kopplingen kan också ha begränsningsgränser. Vissa API:er i Microsoft Exchange Server har till exempel strängare begränsningsgränser än Office 365 Outlook-anslutningen.

Som standard körs en logikapps instanser och eventuella loopar eller grenar inuti dessa instanser *parallellt*. Det här problemet innebär att flera instanser kan anropa samma slutpunkt samtidigt. Varje instans vet inte om den andes existens, så försök att försöka försöka misslyckade åtgärder kan skapa [konkurrensvillkor](https://en.wikipedia.org/wiki/Race_condition) där flera anrop försöker köra samtidigt, men för att lyckas måste dessa anrop komma fram till måltjänsten eller systemet innan begränsningen börjar hända.

Anta till exempel att du har en matris som har 100 objekt. Du använder en "för varje" loop för att iterera genom matrisen och aktivera loopens samtidighetskontroll så att du kan begränsa antalet parallella iterationer till 20 eller den [aktuella standardgränsen](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Inuti den loopen infogar en åtgärd ett objekt från matrisen i en SQL Server-databas, vilket endast tillåter 15 anrop per sekund. Det här scenariot resulterar i ett begränsningsproblem eftersom en eftersläpning av återförsök byggs upp och aldrig får köras.

I den här tabellen beskrivs tidslinjen för vad som händer i loopen när åtgärdens återförsöksintervall är 1 sekund:

| Punkt i tiden | Antal åtgärder som körs | Antal åtgärder som misslyckas | Antal återförsök som väntar |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 sekunder | 20 skär | 5 misslyckas, på grund av SQL-gräns | 5 nya försök |
| T + 0,5 sekunder | 15 skär, på grund av tidigare 5 försök väntar | Alla 15 misslyckas, på grund av tidigare SQL-gräns fortfarande i kraft i ytterligare 0,5 sekunder | 20 försök <br>(tidigare 5 + 15 nya) |
| T + 1 sekund | 20 skär | 5 misslyckas plus tidigare 20 försök, på grund av SQL-gräns | 25 försök (tidigare 20 + 5 nya)
|||||

Om du vill hantera begränsning på den här nivån har du följande alternativ:

* Skapa logikappar så att var och en hanterar en enda åtgärd.

  * Om du fortsätter med exempelscenariot för SQL Server i det här avsnittet kan du skapa en logikapp som placerar matrisobjekt i en kö, till exempel en [Azure Service Bus-kö](../connectors/connectors-create-api-servicebus.md). Du skapar sedan en annan logikapp som bara utför infogningsåtgärden för varje objekt i den kön. På så sätt körs bara en logikappinstans vid en viss tidpunkt, som antingen slutför infogningen och går vidare till nästa objekt i kön, eller så får instansen 429 fel men inte försöker med improduktiva återförsök.

  * Skapa en överordnad logikapp som anropar en underordnad eller kapslad logikapp för varje åtgärd. Om den överordnade måste anropa olika underordnade appar baserat på den överordnades resultat kan du använda en villkorsåtgärd eller byta åtgärd som avgör vilken underordnad app som ska anropas. Det här mönstret kan hjälpa dig att minska antalet samtal eller åtgärder.

    Anta till exempel att du har två logikappar, var och en med en avsökningsutlösare som kontrollerar ditt e-postkonto varje minut för specifikt ämne, till exempel "Framgång" eller "Misslyckande". Den här inställningen resulterar i 120 samtal per timme. Om du skapar en ensamstående överordnad logikapp som avsöker varje minut men anropar en underordnad logikapp som körs baserat på om ämnet är "Lyckades" eller "Misslyckande", minskar du antalet avsökningskontroller till hälften, eller 60 i det här fallet.

* Ställ in batchbearbetning.

  Om måltjänsten stöder batchåtgärder kan du adressera begränsning genom att bearbeta artiklar i grupper eller batchar i stället för individuellt. Om du vill implementera batchbearbetningslösningen skapar du en logikapp för "batchmottagare" och en logikapp för batchavsändare. Batchavsändaren samlar in meddelanden eller objekt tills de angivna villkoren är uppfyllda och skickar sedan dessa meddelanden eller objekt i en enda grupp. Batchmottagaren accepterar den gruppen och bearbetar dessa meddelanden eller objekt. Mer information finns [i Batchprocessmeddelanden i grupper](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Använd webhook-versionerna för utlösare och åtgärder i stället för avsökningsversionerna.

  Varför det? En avsökningsutlösare fortsätter att kontrollera måltjänsten eller målsystemet med specifika intervall. Ett mycket frekvent intervall, till exempel varje sekund, kan skapa begränsningsproblem. En webhook-utlösare eller åtgärd, till exempel [HTTP Webhook,](../connectors/connectors-native-webhook.md)skapar dock bara ett enda anrop till måltjänsten eller målsystemet, vilket sker vid prenumerationstillfället och begär att målet endast meddelar utlösaren eller åtgärden när en händelse inträffar. På så sätt behöver utlösaren eller åtgärden inte kontinuerligt kontrollera målet.
  
  Så om måltjänsten eller systemet stöder webhooks eller tillhandahåller en anslutning som har en webhook-version, är det här alternativet bättre än att använda avsökningsversionen. Om du vill identifiera webhook-utlösare `ApiConnectionWebhook` och åtgärder bekräftar du att de har typen eller att de inte kräver att du anger en upprepning. Mer information finns i [ÅTGÄRDEN APIConnectionWebhook och](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) [ÅTGÄRDEN APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [begränsningar och konfiguration](../logic-apps/logic-apps-limits-and-config.md) av Logic Apps
