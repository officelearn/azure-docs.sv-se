---
title: Azure API Management-terminologi | Microsoft Docs
description: Den här artikeln innehåller definitioner för termer som är specifika för API Management.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074062"
---
# <a name="terminology"></a>Terminologi

Den här artikeln innehåller definitioner för termer som är specifika för API Management (APIM).

## <a name="term-definitions"></a>Termdefinitioner

* **Serverdels-API** – en HTTP-tjänsten som implementerar ditt API och åtgärderna. 
* **Klientdelens API**/**APIM API** -API: er inte värd för en APIM-API, fasader skapas för dina API: er för att anpassa fasaden efter dina behov utan att röra baksidan sluta API. Mer information finns i [importera och publicera en API](import-and-publish.md).
* **APIM-produkten** – en produkt innehåller en eller flera API: er samt en användningskvot och användningsvillkoren. Du kan inkludera flera API: er och erbjuda dem till utvecklare via utvecklarportalen. Mer information finns i [skapa och publicera en produkt](api-management-howto-add-products.md).
* **APIM API-åtgärden** -varje APIM API representerar en uppsättning åtgärder som är tillgängliga för utvecklare. Varje APIM API innehåller en referens till backend-tjänst som implementerar API: et och dess åtgärder mappar till åtgärderna som implementeras av backend-tjänst. Mer information finns i [Fingera API-svar](mock-api-responses.md).
* **Version** – ibland du vill publicera ny eller annan API-funktioner till vissa användare, medan andra vill Håll dig till API: et som för närvarande tycker passar. Mer information finns i [publicera flera versioner av ditt API](api-management-get-started-publish-versions.md).
* **Revision** – när ditt API är redo för att gå och börjar användas av utvecklare du vanligtvis måste du vara försiktig gör ändringar till det API: et och samtidigt till att inte stör anropen av ditt API. Det är också bra att informera utvecklarna om de ändringar du gjort. Mer information finns i [Använd revideringar](api-management-get-started-revise-api.md).
* **Utvecklarportalen** -kunderna (utvecklare) bör använda Developer-portalen för att komma åt dina API: er. Developer-portalen kan anpassas. Mer information finns i [anpassa utvecklarportalen](api-management-customize-styles.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en instans](get-started-create-service-instance.md)

