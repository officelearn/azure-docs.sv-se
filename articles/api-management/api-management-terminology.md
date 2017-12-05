---
title: "Terminologi för Azure API Management | Microsoft Docs"
description: "Den här artikeln innehåller definitioner för de villkor som är specifika för API-hantering."
services: api-management
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 9391b65a5aade4c050ca964354bfea2d3a2338d8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="terminology"></a>Terminologi

Den här artikeln innehåller definitioner för de villkor som är specifika för API Management (APIM).

## <a name="term-definitions"></a>Termdefinitioner

* **Backend-API** -en HTTP-tjänsten som implementerar din API och dess åtgärder. 
* **Klientdel API**/**APIM API** -en APIM API inte är värd för API: er, skapar den facades för dina API: er för att anpassa facade efter dina behov utan att röra baksidan avslutas API. Mer information finns i [Import och publicera en API](import-and-publish.md).
* **APIM produkten** -en produkt som innehåller en eller flera API: er som en kvot för användning och villkor för användning. Du kan innehålla ett antal API: er och erbjuda utvecklare via Developer-portalen. Mer information finns i [skapa och publicera en produkt](api-management-howto-add-products.md).
* **APIM API-åtgärd** -API för varje APIM representerar en uppsättning åtgärder som är tillgänglig för utvecklare. Varje APIM API innehåller en referens till backend-tjänst som implementerar API: et och dess åtgärder mappar till åtgärder som implementerats av tjänst serverdel. Mer information finns i [Mock API-svar](mock-api-responses.md).
* **Version** – ibland du vill publicera ny eller annan API-funktioner till vissa användare, medan andra vill behålla med API som för närvarande fungerar för dem. Mer information finns i [publicera flera versioner av din API](api-management-get-started-publish-versions.md).
* **Revision** – när din API är redo för att gå och startar som ska användas av utvecklare du vanligtvis måste vara försiktig i ändrar den-API: et och samtidigt inte att störa anropare av din API. Det är också användbart att låta utvecklare känna till om de ändringar du gjort. Mer information finns i [använda revisioner](api-management-get-started-revise-api.md).
* **Developer-portalen** -kunderna (utvecklare) ska använda Developer-portalen för att komma åt dina API: er. Developer-portalen kan anpassas. Mer information finns i [anpassa Developer-portalen](api-management-customize-styles.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en instans](get-started-create-service-instance.md)

