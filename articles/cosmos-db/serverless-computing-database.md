---
title: Serverlös databasdatorbehandling med Azure Cosmos DB och Azure Functions
description: Lär dig mer om hur Azure Cosmos DB och Azure Functions kan användas tillsammans för att skapa händelsedrivna serverlösa datorappar.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 079c246f87bb8294f3c7ad6dea3391f5c67ba0ad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985260"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Serverlös databasdatoranvändning med Azure Cosmos DB och Azure Functions

Serverless computing handlar om förmågan att fokusera på enskilda bitar av logik som är repeterbara och tillståndslösa. Dessa bitar kräver ingen infrastrukturhantering och de förbrukar resurser bara för sekunder, eller millisekunder, de körs för. Kärnan i den serverlösa datorrörelsen är funktioner som görs tillgängliga i Azure-ekosystemet av [Azure Functions](https://azure.microsoft.com/services/functions). Om du vill veta mer om andra serverlösa körningsmiljöer i Azure finns [serverlös på](https://azure.microsoft.com/solutions/serverless/) Azure-sidan. 

Med den inbyggda integreringen mellan [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) och Azure Functions kan du skapa databasutlösare, indatabindningar och utdatabindningar direkt från ditt Azure Cosmos DB-konto. Med Hjälp av Azure Functions och Azure Cosmos DB kan du skapa och distribuera händelsedrivna serverlösa appar med åtkomst med låg latens till omfattande data för en global användarbas.

## <a name="overview"></a>Översikt

Med Azure Cosmos DB och Azure Functions kan du integrera dina databaser och serverlösa appar på följande sätt:

* Skapa en händelsedriven **Azure Functions-utlösare för Cosmos DB**. Den här utlösaren är beroende av [ändringsflöden](change-feed.md) för att övervaka din Azure Cosmos-behållare för ändringar. När ändringar görs i en behållare skickas ändringsfeedströmmen till utlösaren, som anropar Azure-funktionen.
* Du kan också binda en Azure-funktion till en Azure Cosmos-behållare med hjälp av en **indatabindning**. Indatabindningar läser data från en behållare när en funktion körs.
* Binda en funktion till en Azure Cosmos-behållare med hjälp av en **utdatabindning**. Utdatabindningar skriver data till en behållare när en funktion är klar.

> [!NOTE]
> Azure Functions-utlösare, indatabindningar och utdatabindningar för Cosmos DB stöds endast för användning med SQL API. För alla andra Azure Cosmos DB-API:er bör du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API.


Följande diagram illustrerar var och en av dessa tre integrationer: 

![Så här integreras Azure Cosmos DB och Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Functions-utlösaren, indatabindningen och utdatabindningen för Azure Cosmos DB kan användas i följande kombinationer:

* En Azure Functions-utlösare för Cosmos DB kan användas med en utdatabindning till en annan Azure Cosmos-behållare. När en funktion utför en åtgärd på ett objekt i ändringsflödet kan du skriva den till en annan behållare (skriva den till samma behållare som den kom från skulle effektivt skapa en rekursiv loop). Du kan också använda en Azure Functions-utlösare för Cosmos DB för att effektivt migrera alla ändrade objekt från en behållare till en annan behållare, med hjälp av en utdatabindning.
* Indatabindningar och utdatabindningar för Azure Cosmos DB kan användas i samma Azure-funktion. Detta fungerar bra i de fall då du vill hitta vissa data med indatabindningen, ändra den i Azure-funktionen och sedan spara den i samma behållare eller en annan behållare, efter ändringen.
* En indatabindning till en Azure Cosmos-behållare kan användas i samma funktion som en Azure Functions-utlösare för Cosmos DB och kan också användas med eller utan utdatabindning. Du kan använda den här kombinationen för att tillämpa aktuell valutaväxlingsinformation (dras in med en indatabindning till en utbytesbehållare) på ändringsflödet av nya order i din kundvagnstjänst. Den uppdaterade kundvagnssumman, med den aktuella valutakonverteringen tillämpad, kan skrivas till en tredje behållare med hjälp av en utdatabindning.

## <a name="use-cases"></a>Användningsfall

Följande användningsfall visar några sätt att få ut det mesta av dina Azure Cosmos DB-data – genom att ansluta dina data till händelsedrivna Azure Functions.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT användningsfall - Azure Functions utlösare och utdatabindning för Cosmos DB

I IoT-implementeringar kan du anropa en funktion när kontrollmotorlampan visas i en ansluten bil.

**Genomförande:** Använda en Azure Functions-utlösare och utdatabindning för Cosmos DB

1. En **Azure Functions-utlösare för Cosmos DB** används för att utlösa händelser relaterade till bilaviseringar, till exempel kontrollmotorlampan som tänds i en ansluten bil.
2. När kontrollmotorlampan kommer skickas sensordata till Azure Cosmos DB.
3. Azure Cosmos DB skapar eller uppdaterar nya sensordatadokument, sedan strömmas dessa ändringar till Azure Functions-utlösaren för Cosmos DB.
4. Utlösaren anropas vid varje dataändring till sensordatainsamlingen, eftersom alla ändringar strömmas via ändringsflödet.
5. Ett tröskeltillstånd används i funktionen för att skicka sensordata till garantiavdelningen.
6. Om temperaturen också är över ett visst värde skickas även en avisering till ägaren.
7. **Utdatabindningen** för funktionen uppdaterar bilposten i en annan Azure Cosmos-behållare för att lagra information om checkmotorhändelsen.

Följande avbildning visar koden som skrivs i Azure-portalen för den här utlösaren.

![Skapa en Azure Functions-utlösare för Cosmos DB i Azure-portalen](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finansiellt användningsfall - Timer-utlösare och indatabindning

I ekonomiska implementeringar kan du anropa en funktion när ett bankkontosaldo faller under ett visst belopp.

**Genomförande:** En timerutlösare med en Azure Cosmos DB-indatabindning

1. Med hjälp av en [timerutlösare](../azure-functions/functions-bindings-timer.md)kan du hämta bankkontobalansinformationen som lagras i en Azure Cosmos-behållare med tidsburslag med hjälp av en **indatabindning**.
2. Om saldot ligger under tröskelvärdet för låg balans som angetts av användaren följer du upp med en åtgärd från Azure-funktionen.
3. Utdatabindningen kan vara en [SendGrid-integrering](../azure-functions/functions-bindings-sendgrid.md) som skickar ett e-postmeddelande från ett tjänstkonto till de e-postadresser som identifierats för var och en av kontona med lågt saldo.

Följande avbildningar visar koden i Azure-portalen för det här scenariot.

![Index.js-fil för en Timer-utlösare för ett finansiellt scenario](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Run.csx-fil för en Timer-utlösare för ett ekonomiskt scenario](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Användningsfall för spel - Azure Functions utlösare och utdatabindning för Cosmos DB 

När en ny användare skapas i spel kan du söka efter andra användare som kanske känner till dem med hjälp av [Azure Cosmos DB Gremlin API](graph-introduction.md). Du kan sedan skriva resultaten till en [Azure Cosmos DB SQL-databas] för enkel hämtning.

**Genomförande:** Använda en Azure Functions-utlösare och utdatabindning för Cosmos DB

1. Med hjälp av en Azure Cosmos [DB-grafdatabas](graph-introduction.md) för att lagra alla användare kan du skapa en ny funktion med en Azure Functions-utlösare för Cosmos DB. 
2. När en ny användare infogas anropas funktionen och sedan lagras resultatet med hjälp av en **utdatabindning**.
3. Funktionen frågar grafdatabasen för att söka efter alla användare som är direkt relaterade till den nya användaren och returnerar den datauppsättningen till funktionen.
4. Dessa data lagras sedan i en Azure Cosmos DB som sedan enkelt kan hämtas av alla frontend-program som visar den nya användaren deras anslutna vänner.

### <a name="retail-use-case---multiple-functions"></a>Användningsfall för återförsäljare - Flera funktioner

När en användare lägger till en artikel i sin varukorg i butiksimplementeringar har du nu flexibiliteten att skapa och anropa funktioner för valfria pipeline-komponenter.

**Genomförande:** Flera Azure Functions-utlösare för Cosmos DB lyssnar på en behållare

1. Du kan skapa flera Azure-funktioner genom att lägga till Azure Functions-utlösare för Cosmos DB till var och en - som alla lyssnar på samma ändringsflöde av kundvagnsdata. Observera att när flera funktioner lyssnar på samma ändringsflöde krävs en ny lånesamling för varje funktion. Mer information om lånesamlingar finns [i Förstå biblioteket Ändra feedprocessor](change-feed-processor.md).
2. När en ny artikel läggs till i en användares kundvagn anropas varje funktion oberoende av ändringsflödet från kundvagnsbehållaren.
   * En funktion kan använda innehållet i den aktuella korgen för att ändra visningen av andra objekt som användaren kan vara intresserad av.
   * En annan funktion kan uppdatera lagersum summan.
   * En annan funktion kan skicka kundinformation för vissa produkter till marknadsavdelningen, som skickar dem en kampanj mailer. 

     Alla avdelningar kan skapa en Azure Functions for Cosmos DB genom att lyssna på ändringsflödet och se till att de inte försenar kritiska orderbearbetningshändelser i processen.

I alla dessa användningsfall, eftersom funktionen har frikopplat själva appen, behöver du inte snurra upp nya appinstanser hela tiden. I stället konfigurerar Azure Functions enskilda funktioner för att slutföra diskreta processer efter behov.

## <a name="tooling"></a>Verktyg

Native integration mellan Azure Cosmos DB och Azure Functions är tillgänglig i Azure-portalen och i Visual Studio 2019.

* I Azure Functions-portalen kan du skapa en utlösare. Instruktioner för snabbstart finns i [Skapa en Azure Functions-utlösare för Cosmos DB i Azure-portalen](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* I Azure Cosmos DB-portalen kan du lägga till en Azure Functions-utlösare för Cosmos DB i en befintlig Azure Function-app i samma resursgrupp.
* I Visual Studio 2019 kan du skapa utlösaren med hjälp av [Azure Functions Tools:](../azure-functions/functions-develop-vs.md)

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Varför välja Azure Functions-integrering för serverlös databehandling?

Azure Functions ger möjlighet att skapa skalbara arbetsenheter eller koncisa element av logik som kan köras på begäran, utan att etablera eller hantera infrastruktur. Genom att använda Azure Functions behöver du inte skapa en fullskalig app för att svara på ändringar i din Azure Cosmos-databas, du kan skapa små återanvändbara funktioner för specifika uppgifter. Dessutom kan du också använda Azure Cosmos DB-data som indata eller utdata till en Azure-funktion som svar på händelser som en HTTP-begäran eller en tidsinnad utlösare.

Azure Cosmos DB är den rekommenderade databasen för din serverlösa datorarkitektur av följande skäl:

* **Omedelbar åtkomst till alla dina data:** Du har detaljerad åtkomst till alla värden som lagras eftersom Azure Cosmos DB [automatiskt indexerar](index-policy.md) alla data som standard och gör dessa index omedelbart tillgängliga. Det innebär att du hela tiden kan fråga, uppdatera och lägga till nya objekt i databasen och få omedelbar åtkomst via Azure Functions.

* **Schemalös**. Azure Cosmos DB är schemalös - så det är unikt kunna hantera alla datautdata från en Azure-funktion. Den här metoden "hantera vad som helst" gör det enkelt att skapa en mängd olika funktioner som alla utdata till Azure Cosmos DB.

* **Skalbart dataflöde**. Dataflöde kan skalas upp och ned direkt i Azure Cosmos DB. Om du har hundratals eller tusentals funktioner som frågar och skriver till samma behållare kan du skala upp [dina RU/s](request-units.md) för att hantera belastningen. Alla funktioner kan fungera parallellt med hjälp av din allokerade RU/s och dina data är garanterat [konsekventa.](consistency-levels.md)

* **Global replikering**. Du kan replikera Azure Cosmos DB-data [runt om i världen](distribute-data-globally.md) för att minska svarstiden, geo-lokalisera dina data närmast där dina användare är. Precis som med alla Azure Cosmos DB-frågor läss data från händelsedrivna utlösare data från Azure Cosmos DB närmast användaren.

Om du vill integrera med Azure Functions för att lagra data och inte behöver djup indexering eller om du behöver lagra bifogade filer och mediefiler, kan [Azure Blob Storage-utlösaren](../azure-functions/functions-bindings-storage-blob.md) vara ett bättre alternativ.

Fördelar med Azure-funktioner: 

* **Händelsedriven**. Azure Functions är händelsedrivna och kan lyssna på en ändringsfeed från Azure Cosmos DB. Detta innebär att du inte behöver skapa lyssnande logik, du bara hålla ett öga på de förändringar du lyssnar på. 

* **Inga gränser**. Funktionerna körs parallellt och tjänsten snurrar upp så många du behöver. Du ställer in parametrarna.

* **Bra för snabba uppgifter**. Tjänsten snurrar upp nya instanser av funktioner när en händelse utlöses och stänger dem så snart funktionen är klar. Du betalar bara för den tid dina funktioner körs.

Om du är osäker på om Flow, Logic Apps, Azure Functions eller WebJobs är bäst för implementeringen läser [du Välj mellan Flow, Logic Apps, Functions och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Nästa steg

Nu ska vi ansluta Azure Cosmos DB och Azure Functions på riktigt: 

* [Skapa en Azure Functions-utlösare för Cosmos DB i Azure-portalen](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Skapa en HTTP-utlösare i Azure Functions med en Azure Cosmos DB-indatabindning](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB-bindningar och utlösare](../azure-functions/functions-bindings-cosmosdb-v2.md)