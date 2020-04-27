---
title: Terminologi för Azure API Management | Microsoft Docs
description: Den här artikeln innehåller definitioner av de termer som är speciella för API Management.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "61074062"
---
# <a name="terminology"></a>Terminologi

Den här artikeln innehåller definitioner av de termer som är speciella för API Management (APIM).

## <a name="term-definitions"></a>Term definitioner

* **Server dels-API** – en http-tjänst som implementerar ditt API och dess åtgärder. 
* **Frontend API**API för frontend-APIM – ett APIM-API är inte värd för API: er, det skapar fasader för dina API: er för att anpassa fasad enligt dina behov utan att behöva röra Server dels-API: et.**APIM API** / Mer information finns i [Importera och publicera ett API](import-and-publish.md).
* **APIM-produkt** – en produkt innehåller en eller flera API: er samt användnings kvot och användnings villkor. Du kan inkludera ett antal API: er och erbjuda dem till utvecklare via Developer-portalen. Mer information finns i [skapa och publicera en produkt](api-management-howto-add-products.md).
* **APIM API-åtgärd** – varje APIM-API representerar en uppsättning åtgärder som är tillgängliga för utvecklare. Varje APIM-API innehåller en referens till Server dels tjänsten som implementerar API: et och dess åtgärder mappar till de åtgärder som implementeras av Server dels tjänsten. Mer information finns i avsnittet om [modeller av API-svar](mock-api-responses.md).
* **Version** – ibland vill du publicera nya eller andra API-funktioner för vissa användare, medan andra vill använda det API som för närvarande fungerar för dem. Mer information finns i [publicera flera versioner av ditt API](api-management-get-started-publish-versions.md).
* **Revision** – när ditt API är klart att användas och börjar användas av utvecklare, behöver du vanligt vis inte tänka på att göra ändringar i detta API och samtidigt inte störa anropen av ditt API. Det är också bra att informera utvecklarna om de ändringar du gjort. Mer information finns i [använda revisioner](api-management-get-started-revise-api.md).
* **Developer-portalen** – dina kunder (utvecklare) bör använda Developer-portalen för att få åtkomst till dina API: er. Developer-portalen kan anpassas. Mer information finns i [Anpassa Developer-portalen](api-management-customize-styles.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en instans](get-started-create-service-instance.md)

