---
title: 'Snabb start: skapa ett Sök index med hjälp av REST API: er'
titleSuffix: Azure Cognitive Search
description: 'I den här REST API snabb starten lär du dig att anropa Azure Kognitiv sökning REST-API: er med hjälp av Postman eller Visual Studio Code.'
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714217"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Snabb start: skapa ett Azure Kognitiv sökning-index med hjälp av REST API: er

Den här artikeln förklarar hur du formulerar REST API begär Anden interaktivt med [Azure KOGNITIV sökning REST-API: er](/rest/api/searchservice) och en API-klient för att skicka och ta emot förfrågningar. Med en API-klient och dessa instruktioner kan du skicka begär Anden och Visa svar innan du skriver någon kod.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du utför grundläggande uppgifter kan du gå vidare med ytterligare REST API-anrop för mer avancerade funktioner, till exempel indexerare eller [skapa en pipeline](cognitive-search-tutorial-blob.md) som lägger till innehålls omvandlingar till indexering. I nästa steg rekommenderar vi följande länk:

> [!div class="nextstepaction"]
> [Självstudie: Använd REST och AI för att generera sökbart innehåll från Azure-blobbar](cognitive-search-tutorial-blob.md)