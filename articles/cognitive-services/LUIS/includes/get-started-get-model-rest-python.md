---
title: 'Skaffa modell med REST-samtal i C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368443"
---
## <a name="prerequisites"></a>Krav

* Azure Language Understanding - Författare resurs 32 teckennyckel och redigering slutpunkt URL. Skapa med [Azure-portalen](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importera [TravelAgent-appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) från GitHub-databasen för kognitiva tjänster-språkförståelse.
* App-id:t i LUIS för den importerade TravelAgent-appen. Program-ID visas på programmets instrumentpanel.
* Den versions-ID i programmet som tar emot yttrandena. Standard-id:t är ”0.1”.
* [Python 3.6](https://www.python.org/downloads/) eller senare.
* [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Ändra modell programmässigt

1. Skapa en ny fil med namnet `model.py`. Lägg till följande kod:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Ersätt de värden `YOUR-` som börjar med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32 tecken författande nyckel.|
    |`YOUR-ENDPOINT`| Slutpunkten för redigerings-URL. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`. Du anger resursnamnet när du skapade resursen.|
    |`YOUR-APP-ID`| Ditt LUIS-app-ID. |

    Tilldelade nycklar och resurser visas i LUIS-portalen i avsnittet Hantera på sidan **Azure-resurser.** App-ID:et är tillgängligt i samma avsnittet Hantera på sidan **Programinställningar.**

1. Med en kommandotolk i samma katalog som när du skapade filen anger du följande kommando för att köra filen:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten tar du bort filen från filsystemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för en app](../luis-concept-best-practices.md)
