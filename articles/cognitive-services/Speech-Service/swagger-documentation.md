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
ms.openlocfilehash: 30e8224b6cb757f044a5eac598d834cee838391e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629989"
---
# <a name="swagger-documentation"></a>Dokumentation om Swagger

Tal tjänsten erbjuder en Swagger-specifikation för att interagera med en fåtal av REST-API: er som används för att importera data, skapa modeller, testa modell precision, skapa anpassade slut punkter, köa batch-avskrifter och hantera prenumerationer. De flesta åtgärder som är tillgängliga via Custom Speech Portal kan slutföras via programmering med dessa API: er.

> [!NOTE]
> Både tal-till-text-och text till tal-åtgärder stöds tillgängliga som REST-API: er, som i sin tur dokumenteras i Swagger-specifikationen.

## <a name="generating-code-from-the-swagger-specification"></a>Generera kod från Swagger-specifikationen

[Swagger-specifikationen](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) har alternativ som gör att du snabbt kan testa olika sökvägar. Ibland är det dock önskvärt att generera kod för alla sökvägar och skapa ett enda bibliotek med anrop som du kan basera framtida lösningar på. Låt oss ta en titt på processen för att skapa ett Python-bibliotek.

Du måste ange Swagger till samma region som din röst tjänst prenumeration. Du kan bekräfta din region i Azure Portal under din röst tjänst resurs. En fullständig lista över regioner som stöds finns i [regioner](regions.md).

1. I en webbläsare går du till Swagger-specifikationen för din region `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0` . Klicka på **API-definition**på sidan och klicka på **Swagger**. Kopiera webb adressen till sidan som visas.
1. I en ny webbläsare går du till https://editor.swagger.io
1. Klicka på **Arkiv**, klicka på **Importera URL**, klistra in URL: en och klicka på **OK**.
1. Klicka på **generera klient** och välj **python**. Klient biblioteket laddas ned till datorn.

Du kan använda python-biblioteket som du genererade med [tal tjänst exemplen på GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referens dokument

* [REST (Swagger): batch-avskrift och anpassning](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: tal till text](rest-speech-to-text.md)
* [REST API: text till tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Tal tjänst exempel på GitHub](https://aka.ms/csspeech/samples).
* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
