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
ms.openlocfilehash: dd9ac9654f916653af974e816485630423466ae5
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815437"
---
# <a name="swagger-documentation"></a>Dokumentation om Swagger

Tal tjänsten erbjuder en Swagger-specifikation för att interagera med en fåtal av REST-API: er som används för att importera data, skapa modeller, testa modell precision, skapa anpassade slut punkter, köa batch-avskrifter och hantera prenumerationer. De flesta åtgärder som är tillgängliga via Custom Speech Portal kan slutföras via programmering med dessa API: er.

> [!NOTE]
> Både tal-till-text-och text till tal-åtgärder stöds tillgängliga som REST-API: er, som i sin tur dokumenteras i Swagger-specifikationen.

## <a name="generating-code-from-the-swagger-specification"></a>Generera kod från Swagger-specifikationen

[Swagger-specifikationen](https://cris.ai/swagger/ui/index) har alternativ som gör att du snabbt kan testa olika sökvägar. Ibland är det dock önskvärt att generera kod för alla sökvägar och skapa ett enda bibliotek med anrop som du kan basera framtida lösningar på. Låt oss ta en titt på processen för att skapa ett Python-bibliotek.

Du måste ange Swagger till samma region som din röst tjänst prenumeration. Du kan bekräfta din region i Azure Portal under din röst tjänst resurs. En fullständig lista över regioner som stöds finns i [regioner](regions.md).

1. Gå till https://editor.swagger.io
2. Klicka på **Arkiv**och sedan på **Importera**
3. Ange Swagger-URL, inklusive regionen för din prenumerations tjänst prenumeration `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klicka på **generera klient** och välj python
5. Spara klient biblioteket

Du kan använda python-biblioteket som du genererade med [tal tjänst exemplen på GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referensdokument

* [REST (Swagger): batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)
* [REST API: tal till text](rest-speech-to-text.md)
* [REST API: text till tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Tal tjänst exempel på GitHub](https://aka.ms/csspeech/samples).
* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
