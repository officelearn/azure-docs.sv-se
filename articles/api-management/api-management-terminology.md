---
title: Terminologi för Azure API Management | Microsoft-dokument
description: Den här artikeln innehåller definitioner för de termer som är specifika för API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61074062"
---
# <a name="terminology"></a>Terminologi

Den här artikeln innehåller definitioner för de termer som är specifika för API Management (APIM).

## <a name="term-definitions"></a>Termdefinitioner

* **Servernings-API** - En HTTP-tjänst som implementerar ditt API och dess åtgärder. 
* **Frontend API**/**APIM API** - Ett APIM API är inte värd api: er, skapar det fasader för dina API: er för att anpassa fasaden efter dina behov utan att röra backend API. Mer information finns i [Importera och publicera ett API](import-and-publish.md).
* **APIM-produkt** - en produkt innehåller en eller flera API:er samt en användningskvot och användarvillkoren. Du kan inkludera ett antal API:er och erbjuda dem till utvecklare via utvecklarportalen. Mer information finns i [Skapa och publicera en produkt](api-management-howto-add-products.md).
* **APIM API-åtgärd** - Varje APIM API representerar en uppsättning åtgärder som är tillgängliga för utvecklare. Varje APIM API innehåller en referens till backend-tjänsten som implementerar API:et och dess åtgärder mappas till de åtgärder som implementeras av serverdelstjänsten. Mer information finns i [Mock API-svar](mock-api-responses.md).
* **Version** - Ibland vill du publicera nya eller olika API-funktioner till vissa användare, medan andra vill hålla sig till API som för närvarande fungerar för dem. Mer information finns i [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md).
* **Revision** - När ditt API är redo att gå och börjar användas av utvecklare, måste du vanligtvis vara försiktig med att göra ändringar i det API och samtidigt inte störa anropare av ditt API. Det är också bra att informera utvecklarna om de ändringar du gjort. Mer information finns i [Använda ändringar](api-management-get-started-revise-api.md).
* **Utvecklarportalen** – Dina kunder (utvecklare) bör använda utvecklarportalen för att komma åt dina API:er. Utvecklarportalen kan anpassas. Mer information finns [i Anpassa utvecklarportalen](api-management-customize-styles.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en instans](get-started-create-service-instance.md)

