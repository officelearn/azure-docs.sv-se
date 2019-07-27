---
title: Dokumentation om Swagger-tal service
titleSuffix: Azure Cognitive Services
description: 'Swagger-dokumentationen kan användas för att automatiskt generera SDK: er för ett antal programmeringsspråk. Alla åtgärder i vår tjänst stöds av Swagger'
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552578"
---
# <a name="swagger-documentation"></a>Dokumentation om Swagger

Tal tjänsterna erbjuder en Swagger-specifikation för att interagera med en fåtal av REST-API: er som används för att importera data, skapa modeller, testa modell precision, skapa anpassade slut punkter, köa batch-avskrifter och hantera prenumerationer. De flesta åtgärder som är tillgängliga via Custom Speech Portal kan slutföras via programmering med dessa API: er.

> [!NOTE]
> Både tal-till-text-och text till tal-åtgärder stöds tillgängliga som REST-API: er, som i sin tur dokumenteras i Swagger-specifikationen.

## <a name="generating-code-from-the-swagger-specification"></a>Generera kod från Swagger-specifikationen

[Swagger](https://cris.ai/swagger/ui/index) -specifikationen har alternativ som gör att du snabbt kan testa olika sökvägar. Ibland är det dock önskvärt att generera kod för alla sökvägar och skapa ett enda bibliotek med anrop som du kan basera framtida lösningar på. Låt oss ta en titt på processen för att skapa ett Python-bibliotek.

Du måste ange Swagger till samma region som din röst tjänst prenumeration. Du kan bekräfta din region i Azure Portal under din resurs för tal tjänster. En fullständig lista över regioner som stöds finns i [regioner](regions.md).

1. Gå till https://editor.swagger.io
2. Klicka på **Arkiv**och sedan på **Importera**
3. Ange Swagger-URL inklusive regionen för din prenumeration på tal tjänster`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klicka på **generera klient** och välj python
5. Spara klient biblioteket

Du kan använda python-biblioteket som du genererade med [Speech Services-exemplen på GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referensdokument

* [REST (Swagger): Batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)
* [REST API: Tal till text](rest-speech-to-text.md)
* [REST API: Text till tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Exempel på tal tjänster på GitHub](https://aka.ms/csspeech/samples).
* [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
