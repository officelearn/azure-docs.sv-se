---
title: "Anpassad kod för Logikappar i Azure med Azure Functions | Microsoft Docs"
description: "Skapa och köra anpassad kod för Logikappar i Azure med Azure Functions"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Lägg till och köra anpassad kod för logic apps via Azure Functions

Om du vill köra anpassade kodavsnitt för C# eller node.js i logikappar, kan du skapa anpassade funktioner via Azure Functions. 
[Azure Functions](../azure-functions/functions-overview.md) erbjuder server-dator i Microsoft Azure och är användbara för att utföra dessa uppgifter:

* Avancerad formatering eller beräknade fält i logikappar
* Utföra beräkningar i ett arbetsflöde.
* Utöka funktionerna för logik-app med funktioner som stöds i C# eller node.js

## <a name="create-custom-functions-for-your-logic-apps"></a>Skapa anpassade funktioner för dina logic apps

Vi rekommenderar att du skapar en funktion i Azure Functions-portalen från den **allmän Webhook - nod** eller **allmän Webhook - C#** mallar. Resultatet skapas en auto-fylls i en mall som accepterar `application/json` från en logikapp. Funktioner som du skapar från dessa mallar identifieras automatiskt och visas i designern logik appen under **Azure Functions i min region.**

I Azure-portalen på den **integrera** fönstret för din funktion mallen ska visas som **läge** inställd på **Webhook** och **Webhook typen** är inställd på **allmänna JSON**. 

Webhook-funktioner acceptera en begäran och skicka det till metoden via en `data` variabel. Du kan komma åt egenskaperna för din nyttolast med hjälp av punktnotation som `data.function-name`. Till exempel en enkel JavaScript-funktion som konverterar ett DateTime-värde till en datumsträng ser ut som i följande exempel:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Anropa Azure Functions från logikappar

För att lista behållare i din prenumeration och välj den funktion som du vill anropa i logik App Designer klickar du på den **åtgärder** -menyn och välj bland **Azure Functions i min Region**.

När du har valt funktionen uppmanas du att ange ett inkommande nyttolast-objekt. Det här objektet är meddelandet att logikappen skickas till funktionen och måste vara ett JSON-objekt. Om du vill skicka i till exempel den **ändrades senast** datum från en Salesforce-utlösare funktionen nyttolasten kan se ut som i det här exemplet:

![Senast ändrad datum][1]

## <a name="trigger-logic-apps-from-a-function"></a>Utlösaren logikappar från en funktion

Du kan utlösa en logikapp inuti en funktion. Se [Logic apps som callable slutpunkter](logic-apps-http-endpoint.md). Skapa en logikapp som har en manuell utlösare och sedan generera från en HTTP POST till manuell utlösaren URL med nyttolast som ska skickas till logikappen i din funktion.

### <a name="create-a-function-from-logic-app-designer"></a>Skapa en funktion från logik App Designer

Du kan också skapa en webhook node.js-funktion från designer. Välj först **Azure Functions i min Region** och välj sedan en behållare för din funktion. Om du ännu inte har en behållare, måste du skapa en från den [Azure Functions-portalen](https://functions.azure.com/signin). Välj sedan **Skapa nytt**.  

Ange context-objektet som du vill skicka till en funktion för att generera en mall baserat på de data som du vill beräkna. Det här objektet måste vara ett JSON-objekt. Till exempel om du skickar i filen innehåll från en FTP-åtgärd, kontext nyttolasten ser ut som det här exemplet:

![Nyttolasten i kontexten][2]

> [!NOTE]
> Eftersom det inte att omvandla det här objektet som en sträng, läggs innehållet direkt till JSON-nyttolast. Dock felmeddelande ett om objektet inte är en JSON-token (det vill säga en sträng eller en JSON-objekt /-matris). Lägg till citattecken för att omvandla objektet som en sträng som visas i den första illustrationen i den här artikeln.
> 

Designern skapar sedan en mall för funktionen att du kan skapa infogad. Variabler skapas före baserat på kontext som du vill skicka till funktionen.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
