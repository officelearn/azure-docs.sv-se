---
title: Databehandling utan Server-databas – Azure Functions och Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB och Azure Functions kan användas tillsammans för att skapa en händelsedriven databehandling utan Server appar.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: ff202c85f20adce173a375987a5f2250fda565b2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041194"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Databas utan Server databehandling med Azure Cosmos DB och Azure Functions

Databearbetning handlar om möjligheten att fokusera på enskilda delar av logik som är upprepningsbar och tillståndslösa. Dessa delar kräver ingen infrastrukturhantering av och de förbrukar resurser endast för sekunder eller millisekunder, de kör för. Kärnan i serverlös databehandling flytt är funktioner som är tillgängliga i Azure-ekosystemet av [Azure Functions](https://azure.microsoft.com/services/functions). Vill veta mer om andra miljöer för serverfri körning i Azure finns i [utan server i Azure](https://azure.microsoft.com/solutions/serverless/) sidan. 

Med den interna integreringen mellan [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) och Azure Functions, du kan skapa databasutlösare, indatabindningar och utdatabindningar direkt från Azure Cosmos DB-kontot. Med Azure Functions och Azure Cosmos DB, kan du skapa och distribuera händelsedrivna appar utan server med låg latens, att utforska data för en global användarbas.

## <a name="overview"></a>Översikt

Azure Cosmos DB och Azure Functions kan du integrera dina databaser och appar utan server på följande sätt:

* Skapa en händelsedriven **Azure Cosmos DB-utlösare** i en Azure-funktion. Den här utlösaren förlitar sig på [ändringsflödet](change-feed.md) strömmar för att övervaka din Azure Cosmos DB-behållare för ändringar. När ändringar görs till en behållare, skickas ändringsflödet stream till utlösare som anropar Azure Function.
* Alternativt kan du binda en Azure-funktion till ett Azure Cosmos DB behållaren med hjälp av en **indatabindning**. Indatabindningar läsa data från en behållare när en funktion körs.
* Binda en funktion till ett Azure Cosmos DB behållaren med hjälp av en **utdatabindning**. Utdatabindningar skriva data till en behållare när en funktion är klar.

> [!NOTE]
> Azure Cosmos DB-utlösaren, indatabindningar och utdatabindningar stöds för närvarande för användning med SQL-API. För alla andra Azure Cosmos DB API: er, bör du komma åt databasen från din funktion med hjälp av statiska klienten för ditt API.


Följande diagram illustrerar var och en av dessa tre integreringar: 

![Hur Azure Cosmos DB och Azure Functions integrerar](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Cosmos DB-utlösare, indatabindning och utdata-bindning kan användas i följande kombinationer:
* En Azure Cosmos DB-utlösare kan användas med en utdatabindning till en annan Azure Cosmos DB-behållare. När en funktion som utför en åtgärd på ett objekt i ändringsflöde kan du skriva det till en annan behållare (skriver den till samma behållare som de kommer ifrån effektivt skapar en rekursiv-loop). Eller du kan använda en Azure Cosmos DB-utlösare för att migrera effektivt alla ändrade objekt från en behållare till en annan behållare med hjälp av en utdatabindning.
* Indatabindningar och utdatabindningar för Azure Cosmos DB kan användas i samma Azure-funktion. Detta fungerar bra i fall när du vill hitta vissa data med indatabindningen, göra ändringar i Azure-funktion och spara den på samma behållare eller en annan behållare efter ändringen.
* En indatabindning till en Azure Cosmos DB-behållare kan användas i samma funktion som en Azure Cosmos DB-utlösare och kan användas med eller utan en utdatabindning samt. Du kan använda den här kombinationen tillämpas uppdaterade valuta exchange-information (samlat in med en indatabindning till en exchange-behållare) ändringsflöde för nya order i ditt i kundvagnen. Uppdaterade i kundvagnen totalen, kan med den aktuella valutakonvertering tillämpas, skrivas till en tredje behållare med hjälp av en utdatabindning.

## <a name="use-cases"></a>Användningsfall

Följande användningsfall visar några sätt som du kan göra de flesta av dina Azure Cosmos DB-data, genom att ansluta dina data till en händelsedriven Azure Functions.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT-användningsfall - Azure Cosmos DB-utlösare och -utdatabindning

I IoT-implementeringar kan anropa du en funktion när Kontrollera motorn ljus visas i en ansluten bil.

**Implementering:** Använda en Azure Cosmos DB-utlösare och en utdatabindning

1. En **Azure Cosmos DB-utlösare** används för att utlösa händelser relaterade till bil aviseringar, till exempel kontrollera motorn ljus som kommer i en ansluten bil.
2. När kontrollen motorn ljus kommer, skickas sensordata till Azure Cosmos DB.
3. Azure Cosmos DB skapar eller uppdaterar nya sensor datadokument och sedan dessa ändringar strömmas till Azure Cosmos DB-utlösare.
4. Utlösaren har anropats på varje dataändring sensor datainsamling, eftersom alla ändringar strömmas via de ändringsflödet.
5. Ett tröskelvärde villkor används i funktionen för att skicka sensordata till garantiavdelningen.
6. Om temperaturen är också över ett visst värde, skickas en avisering också till ägaren.
7. Den **utdatabindning** uppdaterar bil posten i en annan Azure Cosmos DB-behållare för att lagra information om händelsen Kontrollera motorn på funktionen.

Följande bild visar den kod som skrivits i Azure-portalen för den här utlösaren.

![Skapa en Azure Cosmos DB-utlösare i Azure portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finans-användningsfall - Timer som utlösare och indatabindning

I finansiella-implementeringar kan anropa du en funktion när en bank saldo faller under ett visst belopp.

**Implementering:** En timer som utlösare med en Azure Cosmos DB-indatabindning

1. Med hjälp av en [timerutlösare](../azure-functions/functions-bindings-timer.md), du kan hämta den saldo Bankkontouppgifter lagras i en Azure Cosmos DB-behållare med tidsinställda intervall med hjälp av en **indatabindning**.
2. Om saldot är lägre än tröskelvärdet låg balans som anges av användaren, Följ med en åtgärd från Azure-funktion.
3. Utdata-bindning kan vara en [SendGrid integrering](../azure-functions/functions-bindings-sendgrid.md) som skickar ett e-postmeddelande från ett tjänstkonto till de e-postadresser som identifieras för varje konto låg balans.

Följande bilder visar koden i Azure-portalen för det här scenariot.

![Index.js-fil för en Timer som utlösare för en finansiell scenario](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Run.csx-filen för en Timer som utlösare för en finansiell scenario](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Spel användningsfall - Azure Cosmos DB-utlösare och -utdatabindning

I spel, när en ny användare skapas du kan söka efter andra användare som kanske känner till dem med hjälp av den [Gremlin-API för Azure Cosmos DB](graph-introduction.md). Du kan sedan skriva resultaten till en [Azure Cosmos DB SQL-databas] för enkel hämtning.

**Implementering:** Använda en Azure Cosmos DB-utlösare och en utdatabindning

1. Med hjälp av en Azure Cosmos DB [grafdatabas](graph-introduction.md) för att lagra alla användare, kan du skapa en ny funktion med en Azure Cosmos DB-utlösare. 
2. När en ny användare har infogats, funktionen anropas och resultatet lagras med hjälp av en **utdatabindning**.
3. Funktionen frågar grafdatabas att söka efter alla användare som är direkt relaterade till den nya användaren och returnerar datauppsättningen till funktionen.
4. Dessa data lagras sedan i ett Azure Cosmos DB som kan sedan enkelt hämtas alla frontend program som visar den nya användaren deras anslutna vänner.

### <a name="retail-use-case---multiple-functions"></a>Detaljhandel användningsfall - flera funktioner

I retail-implementeringar när en användare lägger till ett objekt i deras korgen har nu du möjlighet att skapa och anropa funktioner för valfritt företag pipeline-komponenter.

**Implementering:** Flera Azure Cosmos DB-utlösare som lyssnar på en behållare

1. Du kan skapa flera Azure-funktioner genom att lägga till Cosmos DB-utlösare för varje - som lyssnar på samma ändringsflödet att handla kundvagn data. Observera att när flera funktioner som lyssnar på samma ändringsflödet, en ny lånsamling måste anges för varje funktion. Mer information om lån samlingar finns i [förstå biblioteket Change Feed Processor](change-feed-processor.md).
2. När ett nytt objekt läggs till i kundvagn användare, anropas varje funktion oberoende av ändringsflödet från behållaren i kundvagnen.
    * En funktion kan använda innehållet i den aktuella korgen för att ändra visning av andra objekt som användaren kan ha intresse.
    * En annan funktion kan uppdatera inventering summor.
    * En annan funktion kan skicka kundinformation för vissa produkter till marknadsföringsavdelningen som skickar dem till ett kampanjpris avsändarens. 

    En avdelning som kan skapa en Azure Cosmos DB-utlösare genom att lyssna på ändringsflöde och se till att de inte fördröjning kritiska ordning bearbeta händelser i processen.

I alla dessa användningsfall, eftersom funktionen har fristående själva appen, behöver du inte skapa nya appinstanser när som helst. I stället startar Azure Functions enskilda funktioner för att slutföra diskreta processer efter behov.

## <a name="tooling"></a>Verktyg

Intern integrering mellan Azure Cosmos DB och Azure Functions är tillgänglig i Azure-portalen och i Visual Studio 2017.

* Du kan skapa en Azure Cosmos DB-utlösare i Azure Functions-portalen. Snabbstart anvisningar finns i [skapa en Azure Cosmos DB-utlösare i Azure-portalen](https://aka.ms/cosmosdbtriggerportalfunc).
* I Azure Cosmos DB-portalen kan du lägga till en Azure Cosmos DB-utlösare i en befintlig Azure Function-app i samma resursgrupp.
* I Visual Studio 2017, kan du skapa ett Azure Cosmos DB-utlösare med den [Azure Functions Tools för Visual Studio 2017](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Varför ska jag välja Azure Functions-integrering för databearbetning utan Server?

Azure Functions erbjuder möjligheten att skapa skalbara lagringsenheter arbets- eller kortfattade delar som kan köras på begäran, utan att tillhandahålla eller hantera infrastruktur. Med Azure Functions kan du behöver inte skapa en fullständig app för att svara på ändringar i din Azure Cosmos DB-databas, kan du skapa små återanvändbara funktioner för specifika uppgifter. Dessutom kan du också kan använda Azure Cosmos DB-data som indata eller utdata till en Azure-funktion som svar på händelsen, till exempel en HTTP-begäranden eller tidsinställd utlösare.

Azure Cosmos DB är den rekommendera databasen för din serverlös databehandling arkitektur av följande skäl:

* **Omedelbar åtkomst till alla dina data**: Du har detaljerad åtkomst till varje värde som lagras eftersom Azure Cosmos DB [indexerar automatiskt](index-policy.md) alla data som standard och gör dessa index omedelbart tillgänglig. Det innebär att du ska kunna ständigt fråga, uppdatera, och Lägg till nya objekt i databasen och ha omedelbar åtkomst via Azure Functions.

* **Schemalös**. Azure Cosmos DB är schemalös - så att det är unikt kan hantera alla data utdata från en Azure-funktion. Den här ”hanterar allt”-metoden gör det enkelt att skapa en mängd funktioner som alla utdata till Azure Cosmos DB.

* **Skalbart dataflöde**. Dataflöde kan skalas upp och ned direkt i Azure Cosmos DB. Om du har hundratals eller tusentals Functions fråga och skriver till samma behållare kan du kan skala upp din [RU/s](request-units.md) att hantera belastningen. Alla funktioner fungerar parallellt med din allokerade RU/s och dina data är garanterat [konsekvent](consistency-levels.md).

* **Global replikering**. Du kan replikera data i Azure Cosmos DB [i hela världen](distribute-data-globally.md) som minskar svarstiderna, geoplacerar dina data som är närmast dina användare finns där. Som med alla Azure Cosmos DB läses data från händelsedrivna utlösare data från Azure Cosmos DB som är närmast användaren.

Om du vill integrera med Azure Functions för att lagra data och behöver inte djupgående indexering eller om du behöver att lagra bifogade filer och mediafiler, den [Azure Blob Storage-utlösare](../azure-functions/functions-bindings-storage-blob.md) kan vara ett bättre alternativ.

Fördelar med Azure Functions: 

* **Händelsedrivna**. Azure Functions är en händelsedriven och kan lyssna på en ändringsflödet från Azure Cosmos DB. Det här innebär att du inte behöver skapa lyssnande logik, du bara hålla utkik efter ändringar som du lyssnar för. 

* **Ingen gräns**. Funktioner som körs parallellt och tjänsten varv upp så många som du behöver. Du kan ange parametrarna.

* **Bra för Snabbuppgifter**. Tjänsten startar nya instanser av funktioner när en händelse utlöses och stänger dem när funktionen har slutförts. Du betalar bara för den tid som dina funktioner körs.

Om du inte vet om Flow, Logic Apps, Azure Functions eller WebJobs är bäst för din implementering finns i [Välj mellan Flow, Logic Apps, Functions och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Nästa steg

Nu ska vi ansluta Azure Cosmos DB och Azure Functions riktiga: 

* [Skapa en Azure Cosmos DB-utlösare i Azure portal](https://aka.ms/cosmosdbtriggerportalfunc)
* [Skapa en Azure Functions HTTP-utlösare med en Azure Cosmos DB-indatabindning](https://aka.ms/cosmosdbinputbind)
* [Azure Cosmos DB-bindningar och utlösare](../azure-functions/functions-bindings-cosmosdb.md)


 



