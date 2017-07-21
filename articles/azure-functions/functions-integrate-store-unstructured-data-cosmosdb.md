---
title: "Lagra ostrukturerade data i Azure Cosmos-databasen med hjälp av funktioner"
description: "Lagra ostrukturerade data i Azure Cosmos-databasen med hjälp av funktioner"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "azure-funktioner, funktioner, händelsebearbetning, Cosmos DB, dynamisk beräkning, serverlös arkitektur"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/08/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 492c916a493bb8d5c5415fc517506e5c1ccffc56
ms.contentlocale: sv-se
ms.lasthandoff: 07/10/2017

---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Lagra ostrukturerade data i Azure Cosmos-databasen med hjälp av funktioner

Azure Cosmos-DB är ett bra sätt att lagra ostrukturerade och JSON-data. I kombination med Azure Functions, gör Cosmos DB lagring av data snabbt och enkelt med mycket mindre kod än vad som krävs för att lagra data i en relationsdatabas.

Den här kursen går igenom hur du använder Azure Portal för att skapa en Azure-funktion som lagrar ostrukturerade data i ett Cosmos-DB-dokument. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>Skapa en funktion

Skapa ett nytt C# allmänt WebHook med namnet `MyTaskList`.

1. Expandera listan befintliga funktioner och klicka på plustecknet för att skapa en ny funktion
1. Välj GenericWebHook CSharp och ge den namnet `MyTaskList`

![Lägg till ny C# allmän WebHook-funktionsapp](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

En Azure-funktion kan ha en utlösare och valfritt antal indata- eller utdatabindningar. I det här exemplet använder vi en HTTP-begäran-utlösare och Cosmos-DB-dokumentet som utdata-bindning.

1. Klicka på funktionsfliken *Integrera* om du vill visa eller ändra funktionens utlösare och bindningar.
1. Välj länken *Nya utdata* uppe till höger på sidan.

Obs: HTTP-begäran-utlösaren har redan konfigurerats, dock måste du lägga till bindning för Cosmos-DB-dokument.

![Lägga till nya Cosmos-DB-utdatabindning](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. Ange informationen som krävs för skapa bindningen. Använd tabellen nedan för att fastställa värden.

![Konfigurera Cosmos-DB-utdatabindning](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  Fält | Värde  |
|---|---|
| Dokumentparameternamn | Namn som refererar till Cosmos-DB-objektet i koden |
| Databasnamn | Namnet på databasen där dokumenten ska sparas |
| Samlingsnamn | Namnet på grupperingen av Cosmos-DB-databaser |
| Vill du att Cosmos-databasen och samlingen skapas automatiskt | Ja eller nej |
| Cosmos DB kontoanslutning | Anslutningssträngen som pekar på Cosmos-DB-databasen |

Du måste också konfigurera anslutningen till Cosmos-DB-databasen.

1. Klicka på länken ”Nytt” bredvid etiketten * Cosmos-DB dokumentetanslutning ”.
1. Fyll i fälten och välj sedan lämpliga inställningar som behövs för att skapa Cosmos-DB-dokumentet.

![Konfigurera Cosmos-DB-anslutning](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  Fält | Värde  |
|---|---|
| Id | Unikt ID för Cosmos-DB-databas  |
| NoSQL API | Cosmos DB eller MongoDB  |
| Prenumeration | MSDN-prenumeration  |
| Resursgrupp  | Skapa en ny resursgrupp eller välj en befintlig.  |
| Plats  | Västeuropa  |

1. Klicka på knappen *OK*. Du kan behöva vänta några minuter medan Azure skapar resurserna.
1. Klicka på knappen *Spara*.

## <a name="update-the-function-code"></a>Uppdatera funktionskoden

Ersätt funktionens mallkod med följande:

Observera att koden för det här exemplet bara använder C#.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

Det här kodexemplet läser HTTP-frågesträngar och tilldelar dem som medlemmar i ett `taskDocument`-objekt. `taskDocument`-objektet sparar data automatiskt i databasen Cosmos DB och även skapar databasen på den första användningen.

## <a name="test-the-function-and-database"></a>Testa funktionen och databasen

1. På fliken funktion, klicka på länken *Test* på höger sida av portalen och ange följande HTTP-frågesträngar:

| Frågesträng | Värde |
|---|---|
| namn | Chris P. Bacon |
| aktivitet | Gör en smörgås |
| duedate | 05/12/2017 |

1. Klicka på den *länken* kör .
1. Kontrollera att funktionen returnerade svarskoden *HTTP 200 OK*.

![Konfigurera Cosmos-DB-utdatabindning](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Kontrollera att en post har skapats i Cosmos-DB-databasen.

1. Hitta din databas i Azure-portalen och markera den.
1. Välj alternativet *Data Explorer*.
1. Expandera noderna tills du når dokumentets poster.
1. Bekräfta databasposten. Det finns ytterligare metadata i databasen tillsammans med dina data.

![Kontrollera Cosmos-DB-posten](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Om dina data finns i dokumentet har du skapat en Azure-funktion som lagrar ostrukturerade data i en Cosmos-DB-databas.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Mer information om att binda till en Cosmos DB-databas finns i [Azure Functions Cosmos DB-bindningar](functions-bindings-documentdb.md).

