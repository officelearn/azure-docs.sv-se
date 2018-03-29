---
title: Serverlösa databasen computing - Azure Functions och Azure Cosmos DB | Microsoft Docs
description: Lär dig hur Azure Cosmos DB och Azure Functions kan användas tillsammans att skapa händelsedriven serverlösa IT-appar.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: mimig
ms.openlocfilehash: 39155a2407efa0cc13236a1bbbbcd292daac86b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: Computing serverlösa databasen med hjälp av Azure-funktioner

Serverlösa computing handlar om möjligheten att fokusera på enskilda delar av logik som är repeterbara och tillståndslösa. Dessa uppgifter kräver ingen infrastrukturhantering och de förbrukar resurser endast för sekunder och millisekunder de körs för. Har funktioner som görs tillgängliga i Azure-ekosystemet av kärnan i serverlösa databehandling flytt [Azure Functions](https://azure.microsoft.com/services/functions).

Med intern integrering mellan [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) och Azure Functions, du kan skapa databasutlösare, bindningar för indata och utdata bindningar direkt från Azure DB som Cosmos-konto. Använder Azure Functions och Azure Cosmos DB kan du skapa och distribuera händelsedriven serverlösa appar med låg latens åtkomst till omfattande data för en global användarbas.

## <a name="overview"></a>Översikt

Azure Cosmos-databas och Azure Functions kan du integrera dina databaser och serverlösa appar på följande sätt:

* Skapa en händelsedriven **Azure Cosmos DB utlösaren** i en Azure-funktion. Den här utlösaren förlitar sig på [ändra feed](change-feed.md) dataströmmar att övervaka din Azure DB som Cosmos-behållare för ändringar. När ändringar görs i en behållare, skickas ändringen mata dataströmmen till utlösare som anropar Azure-funktion.
* Du kan också binda en Azure-funktion till en Azure Cosmos DB samlingen med en **inkommande bindningen**. Inkommande bindningar läsa data från en behållare när en funktion utförs.
* Binda en funktion till en Azure Cosmos DB samlingen med en **utdatabindning**. Utdata bindningar skriva data till en behållare när en funktion har slutförts.

> [!NOTE]
> Just nu fungerar Azure Cosmos DB-utlösaren, indatabindningar och utdatabindningar endast med SQL API och Graph API-konton.

Följande diagram visar var och en av dessa tre integreringar: 

![Hur Azure Cosmos DB och Azure Functions integrera](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure DB som Cosmos-utlösare, indatabindning och utdata bindning kan användas i följande kombinationer:
* En Azure DB som Cosmos-utlösare kan användas med en output-bindning till en annan Azure DB som Cosmos-behållare. När en funktion som utför en åtgärd på ett objekt i denna feed genom ändra skriver du den till en annan behållare (skriver den till den behållare som det kom från effektivt skulle skapa en rekursiv-loop). Eller, du kan använda en Azure DB som Cosmos-utlösare för att effektivt migrera alla ändrade objekt från en behållare till en annan behållare med hjälp av en bindning för utdata.
* Bindningar för inkommande och utgående bindningar för Azure Cosmos DB kan användas i samma Azure-funktion. Detta fungerar bra i fall när du vill söka efter vissa data med den inkommande bindningen, ändra i Azure-funktion och spara den på samma behållare eller en annan behållare efter ändringen.
* En inkommande bindning till en Azure DB som Cosmos-behållare kan användas i samma funktion som en Azure DB som Cosmos-utlösare och kan användas med eller utan bindning samt utdata. Du kan använda den här kombinationen tillämpa uppdaterade valuta utbyta information (som hämtas in med en inkommande bindning till en exchange-behållare) i flödet för ändring av nya order i kundvagn i tjänsten. Uppdaterad i kundvagn total kan med den aktuella valutakonvertering tillämpas, skrivas till en tredje behållare med hjälp av en bindning för utdata.

## <a name="use-cases"></a>Användningsfall

Följande användningsområden visas några sätt som du kan göra mest av dina data i Azure DB som Cosmos - genom att ansluta dina data till händelsedriven Azure Functions.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT-användningsfall - Azure Cosmos DB utlösare och utdataparametrar bindning

Du kan anropa en funktion när Kontrollera motorn ljus visas i en ansluten bil i IoT-implementeringar.

**Implementering:** använder en Azure DB som Cosmos-utlösare och en bindning för utdata

1. En **Azure Cosmos DB utlösaren** används för att utlösa händelser relaterade till bil aviseringar, till exempel kontrollera motorn ljust kommer en bil som är anslutna.
2. När kontrollen motorn ljus kommer skickas dessa sensordata till Azure Cosmos DB.
3. Azure Cosmos-DB skapar eller uppdaterar nya sensor datadokument och sedan ändringarna strömmas till Azure DB som Cosmos-utlösaren.
4. Utlösaren anropas vid varje dataändring sensor datainsamling, eftersom alla ändringar som strömmas via ändringen feed.
5. Ett tröskelvärde villkor används i funktionen sensordata ska skickas till avdelningen garanti.
6. Om är också över ett visst värde, skickas även en avisering till ägaren.
7. Den **utdatabindning** på funktionen uppdaterar bil posten i en annan Azure Cosmos DB samling att lagra information om händelsen Kontrollera motorn.

Följande bild visar den kod som skrivs i Azure-portalen för denna utlösare.

![Skapa en Azure DB som Cosmos-utlösare i Azure-portalen](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finansiella användningsfall - Timer som utlösare och ange bindning

Du kan anropa en funktion när en bank saldo sjunker under en viss mängd finansiella implementeringar.

**Implementering:** en timer som utlösare med en Azure-Cosmos-DB inkommande bindningen

1. Med hjälp av en [timer som utlösare](../azure-functions/functions-bindings-timer.md), du kan hämta konto saldoinformationen som lagras i en Azure DB som Cosmos-behållare med tidsinställda intervall med en **inkommande bindningen**.
2. Om saldot är lägre än tröskelvärdet lågt saldo som anges av användaren, följer du med en åtgärd från Azure-funktion.
3. Utdata bindningen kan vara en [SendGrid integrering](../azure-functions/functions-bindings-sendgrid.md) som skickar ett e-postmeddelande från ett tjänstkonto till e-postadresser identifieras för varje lågt Saldo-konton.

Följande bilder visar koden i Azure-portalen för det här scenariot.

![Index.js-filen för en Timer som utlösare för en finansiell scenario](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Run.csx-filen för en Timer som utlösare för en finansiell scenario](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Spel användningsfall - Azure Cosmos DB utlösare och utdatabindning

I spel, när en ny användare skapas du kan söka efter andra användare som kanske känner till dem med hjälp av den [Azure Cosmos DB Graph API](graph-introduction.md). Du kan sedan skriva resultaten till en [Azure Cosmos-Databasens SQL-databas] enkelt kan användas.

**Implementering:** använder en Azure DB som Cosmos-utlösare och en bindning för utdata

1. Med hjälp av en Azure-Cosmos-DB [diagram databasen](graph-introduction.md) för att lagra alla användare, kan du skapa en ny funktion med en Azure DB som Cosmos-utlösare. 
2. När en ny användare infogas, anropas funktionen och resultatet lagras med en **utdatabindning**.
3. Funktionen fråga graph-databasen för att söka efter alla användare som är direkt relaterade till den nya användaren och returnerar den dataset till funktionen.
4. Dessa data lagras sedan i en Azure Cosmos-databas som kan sedan enkelt hämtas alla frontend program som visar den nya användaren sina anslutna vänner.

### <a name="retail-use-case---multiple-functions"></a>Användningsfall Retail - flera funktioner

I retail-implementeringar när en användare lägger till ett objekt till deras korg har nu du möjlighet att skapa och anropa funktioner för valfri business pipeline-komponenter.

**Implementering:** flera Azure Cosmos DB utlösare lyssnar på en samling

1. Du kan skapa flera Azure-funktioner genom att lägga till Azure Cosmos DB utlösare för varje - som lyssnar på samma ändra feed att handla kundvagn data. Observera att när flera funktioner lyssna på samma ändra feed, en ny samling lån krävs för varje funktion. Mer information om lease samlingar finns [förstå ändra Feed Processor biblioteket](change-feed.md#understand-cf).
2. När ett nytt objekt läggs till i kundvagn användare anropade varje funktion oberoende av ändringen feed från behållaren i kundvagn.
    * En funktion kan använda innehållet i den aktuella korgen vill ändra visningen av andra objekt som användaren kan ha intresse av.
    * En annan funktion kan uppdatera inventering summor.
    * En annan funktion kan skicka kundinformation för vissa produkter till marknadsföringsavdelningen som skickar dem en erbjudanden adresshuvud. 

    En avdelning som kan skapa en Azure DB som Cosmos-utlösare genom att lyssna på Ändra feeden och se till att de inte fördröjning kritiska ordning bearbeta händelser i processen.

I alla dessa användningsfall, eftersom funktionen har fristående själva appen, behöver du inte få igång nya app instanser hela tiden. Azure Functions startar i stället enskilda funktioner för att slutföra diskreta processer efter behov.

## <a name="tooling"></a>Tooling

Intern integrering mellan Azure Cosmos DB och Azure Functions är tillgängliga i Azure-portalen och i Visual Studio-2017.
* Du kan skapa en Azure DB som Cosmos-utlösare i Azure Functions-portalen. Snabbstart instruktioner finns i [skapa en Azure DB som Cosmos-utlösare i Azure portal](https://aka.ms/cosmosdbtriggerportalfunc) ![skapa en Azure DB som Cosmos-utlösare i Azure Functions-portalen](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* I Azure Functions-portalen kan du också lägga till Azure Cosmos DB inkommande bindningar och utdata bindningar till andra typer av utlösare. Snabbstart instruktioner finns i [lagra Ostrukturerade data med hjälp av Azure Functions och Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Skapa en Azure DB som Cosmos-utlösare i Azure Functions-portalen](./media/serverless-computing-database/function-portal-input-binding.png)
*   Du kan lägga till en Azure DB som Cosmos-utlösare i en befintlig app i Azure-funktion i samma resursgrupp i Azure DB som Cosmos-portalen.
    ![Skapa en Azure DB som Cosmos-utlösare i Azure Functions-portalen](./media/serverless-computing-database/cosmos-db-portal.png)
* Du kan skapa en Azure DB som Cosmos-utlösare med mallen integrerad i Visual Studio 2017:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]


## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Varför ska jag välja Azure Functions-integrering för serverlösa beräkningsresurser?

Azure Functions erbjuder möjligheten att skapa skalbara enheter av arbets- eller kortfattade delar som kan köras på begäran, utan att etablera eller hantera infrastrukturen. Med hjälp av Azure Functions kan du behöver inte skapa en komplett app för att svara på förändringar i din Azure Cosmos-DB-databas, kan du skapa små återanvändbara funktioner för specifika uppgifter. Du kan dessutom också använda Azure Cosmos DB data som indata eller utdata till en Azure-funktion som svar på händelsen, till exempel en HTTP förfrågningar eller en tidsinställd utlösare.

Azure Cosmos-DB är den rekommenderade databasen för din serverlösa databehandling arkitektur av följande skäl:

* **Omedelbar åtkomst till dina data**: du har detaljerade åtkomst till varje värde lagras eftersom Azure Cosmos DB [indexerar automatiskt](indexing-policies.md) alla data som standard och gör dessa index omedelbart tillgänglig. Det innebär att du ska kunna ständigt fråga, uppdatera, och Lägg till nya objekt i databasen och omedelbar åtkomst via Azure Functions.

* **Schemalös**. Azure Cosmos-DB är schemalös - så att det är unikt kan hantera alla data från en Azure-funktion. Den här metoden ”hantera allt” gör det enkelt att skapa en mängd funktioner som utdata till Azure Cosmos DB.

* **Skalbara dataflöden**. Dataflöde kan skalas upp och ned direkt i Azure Cosmos-databasen. Om du har hundratals eller tusentals funktioner fråga och skriva till samma samling kan du kan skala upp din [RU/s](request-units.md) hantera belastningen. Alla funktioner fungerar parallellt med din allokerade RU/s och dina data är garanterat [konsekvent](consistency-levels.md).

* **Globala replikering**. Du kan replikera Azure Cosmos DB data [över hela världen](distribute-data-globally.md) att minska svarstiden, geo hitta dina data som är närmast var dina användarna finns. Som med alla Azure Cosmos DB frågor är data från händelsedriven utlösare läsa data från Azure Cosmos DB som är närmast användaren.

Om du vill integrera med Azure Functions för att lagra data och behöver inte djupgående indexering eller om du vill lagra bilagor och mediefiler, den [Azure Blob Storage-utlösaren](../azure-functions/functions-bindings-storage-blob.md) kan vara ett bättre alternativ.

Fördelar med Azure Functions: 

* **Händelsedriven**. Azure Functions är händelsedriven och kan lyssna på en ändring från Azure DB som Cosmos-feed. Det innebär som du inte behöver skapa lyssnande logik du bara Håll utkik efter ändringar du lyssnar för. 

* **Inga begränsningar**. Funktioner som körs parallellt och tjänsten varv upp så många som du behöver. Du kan ange parametrar.

* **Bra för snabb uppgifter**. Tjänsten startar nya instanser av funktioner när en händelse utlöses och stänger dem så snart funktionen slutförs. Du betalar bara för den tid som kör dina funktioner.

Om du inte vet om flödet Logic Apps, Azure Functions eller WebJobs är bäst för din implementering finns [Välj mellan flödet, Logic Apps, funktioner och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Nästa steg

Nu ska vi ansluta Azure Cosmos DB och Azure Functions riktigt: 

* [Skapa en Azure DB som Cosmos-utlösare i Azure-portalen](https://aka.ms/cosmosdbtriggerportalfunc)
* [Skapa en Azure Functions HTTP-utlösare med Azure DB som Cosmos-indatabindning](https://aka.ms/cosmosdbinputbind)
* [Lagra Ostrukturerade data med hjälp av Azure Functions och Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Azure DB Cosmos-bindningar och utlösare](../azure-functions/functions-bindings-cosmosdb.md)


 



