---
title: Hur du använder Azure Cosmos DB-ändringsflödet med Azure Functions
description: Använd Azure Cosmos DB-ändringsflödet med Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 41cbb657a4fc83b498c5cc9a6a16397a619aa075
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034054"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Hur du använder Azure Cosmos DB-ändringsflödet med Azure Functions

Om du använder Azure Functions, det enklaste sättet att ansluta till ändringsflödet är att lägga till en [Azure Cosmos DB-utlösare](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) till din Azure Functions-app. När du skapar en Cosmos DB-utlösare i en Azure Functions-app, väljer du Cosmos-behållare för att ansluta till och funktionen som utlöses när du ändrar något i behållaren.

Utlösare kan skapas i Azure Functions-portalen eller i Azure Cosmos DB-portalen eller via programmering. Mer information finns i [databas utan Server databehandling med Azure Cosmos DB och Azure Functions](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Hur konfigurerar jag Azure functions för att läsa från en viss region?

Det är möjligt att definiera den [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) när du använder Azure Cosmos DB-utlösare för att ange en lista över regioner. Det är samma när du anpassar ConnectionPolicy om du vill att utlösaren ska läsa från ditt önskade regioner. Vi rekommenderar att du vill läsa från den närmaste regionen där dina Azure-funktioner har distribuerats.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Vad är standardstorleken för batchar i Azure Functions?

Standardstorleken är 100 objekten för varje anrop av Azure Functions. Det här talet kan dock konfigureras i function.json-filen. Här är klar [lista med konfigurationsalternativ](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Om du utvecklar lokalt måste du uppdatera programinställningar i filen local.settings.json.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Jag övervakar en behållare och läsa dess ändringsfeed, men jag får alla infogade dokument, saker saknas?

Se till att det finns inga andra Azure-funktion läser samma behållare med samma lånet behållare. Saknas dokumenten bearbetas av de andra Azure-funktioner som också använder samma lånet.

Om du skapar flera Azure Functions om du vill läsa samma ändringsflödet måste de därför använda olika lånet behållare eller använder konfigurationen ”leasePrefix” för att dela samma behållare. När du använder biblioteket change feed processor du kan starta flera instanser av din Azure-funktion och SDK: N delar dokument mellan olika instanser automatiskt åt dig.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Azure Cosmos-objekt uppdateras varje sekund och jag inte får alla ändringar i Azure Functions lyssna på ändringsflödet?

Azure Functions avsöker ändringsflödet ändringar kontinuerligt med en standard för maximal fördröjning på 5 sekunder. Om det finns några väntande ändringar som ska läsas, eller om det finns väntande ändringar när utlösaren används, läses funktionen dem direkt. Men om det finns några väntande ändringar, ska funktionen vänta fem sekunder och söka efter fler ändringar.

Om dokumentet tar emot flera ändringar i samma intervall som tog utlösare för att söka efter nya ändringar, kan du få den senaste versionen av dokumentet och inte det mellanliggande.

Om du vill söka i ändringsflödet för mindre än 5 sekunder till exempel för varje sekund, du kan konfigurera avsökningstiden ”feedPollDelay”, se [hela konfigurationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Den har definierats i millisekunder med standardvärdet 5000. Avsökning för mindre än 1 sekund är möjligt, men det rekommenderas inte eftersom du ska börja använda mer CPU-minne.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Flera Azure Functions kan läsa ändringsfeed för en behållare?

Ja. Flera Azure Functions kan läsa samma behållare ändringsfeed. Azure Functions måste dock ha en separat ”leaseCollectionPrefix” definierats.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Om jag är bearbetning ändringsflödet med Azure Functions, i en batch med 10 dokument, och det uppstår ett fel vid sjunde dokumentet. I så fall bearbetas inte de tre sista dokument hur startar jag bearbetning från det misslyckade dokumentet (dvs, sjunde dokument) i mitt nästa flödet?

Om du vill hantera felet är rekommenderat mönster att omsluta din kod med trycatch-block och, om du iterera över listan med dokument, omsluter varje iteration i sin egen trycatch-block. Fånga felet och placera det dokumentet i en kö (förlorade) och sedan definiera logik för att hantera dokument som fel. Med den här metoden om du har en 200-dokumentet batch och ett enda dokument som misslyckades och behöver du inte slänga hela batchen.

Om felet, inte bör du bakåt kontrollpunkt tillbaka till början kommer annars du kan ständigt dessa dokument från ändringsfeed. Kom ihåg att ändra feed är fortfarande den senaste sista snapin som visar dokument, på grund av det du kan förlora tidigare ögonblicksbild på dokumentet. ändringsfeed ser till att endast en senaste versionen av dokumentet och mellan andra processer kan komma och ändra dokumentet.

Som du hålla åtgärda koden, hittar du snart inga dokument på obeställbara meddelanden. Azure Functions kallas automatiskt genom att ändra feed system och kontrollpunkt underhålls internt av Azure-funktion. Om du vill återställa kontrollpunkt och styra alla aspekter av det, bör du med hjälp av ändringen feed Processor SDK.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Finns det någon extra kostnader för att använda Azure Cosmos DB-utlösare?

Azure Cosmos DB-utlösare använder biblioteket Change Feed Processor internt. Därför krävs ett extra samling som heter lånsamlingen, för att upprätthålla tillstånd och partiella kontrollpunkter. Den här tillståndshantering krävs för att kunna dynamiskt skala och Fortsätt om du vill stoppa dina Azure-funktioner och fortsätta bearbetningen vid ett senare tillfälle. Mer information finns i [hur du arbetar med ändringen feed processor-biblioteket](change-feed-processor.md).

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Översikt över ändringsfeed](change-feed.md)
* [Sätt att läsa ändringsflödet](read-change-feed.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
* [Hur du arbetar med ändringen feed processor-biblioteket](change-feed-processor.md)
* [Databas utan Server databehandling med Azure Cosmos DB och Azure Functions](serverless-computing-database.md)
