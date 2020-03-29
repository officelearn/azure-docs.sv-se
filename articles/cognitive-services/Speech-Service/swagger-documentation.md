---
title: Swagger dokumentation - Taltjänst
titleSuffix: Azure Cognitive Services
description: Swagger-dokumentationen kan användas för att automatiskt generera SDK:er för ett antal programmeringsspråk. All verksamhet i vår tjänst stöds av Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430813"
---
# <a name="swagger-documentation"></a>Dokumentation om Swagger

Taltjänsten erbjuder en Swagger-specifikation för att interagera med en handfull REST-API:er som används för att importera data, skapa modeller, testa modellnoggrannhet, skapa anpassade slutpunkter, köa batchavskrifter och hantera prenumerationer. De flesta åtgärder som är tillgängliga via portalen Anpassat tal kan slutföras programmässigt med hjälp av dessa API:er.

> [!NOTE]
> Både Tal-till-text- och text-till-tal-åtgärder stöds som REST-API:er, vilket i sin tur dokumenteras i Swagger-specifikationen.

## <a name="generating-code-from-the-swagger-specification"></a>Generera kod från Swagger-specifikationen

[Swagger-specifikationen](https://cris.ai/swagger/ui/index) har alternativ som gör att du snabbt kan testa för olika banor. Men ibland är det önskvärt att generera kod för alla sökvägar, skapa ett enda bibliotek med anrop som du kan basera framtida lösningar på. Låt oss ta en titt på processen för att generera ett Python-bibliotek.

Du måste ange Swagger till samma region som din taltjänstprenumeration. Du kan bekräfta din region i Azure-portalen under din taltjänstresurs. En fullständig lista över regioner som stöds finns i [regioner](regions.md).

1. Gå till https://editor.swagger.io
2. Klicka på **Arkiv**och sedan på **Importera**
3. Ange swagger-URL:en, inklusive regionen för din taltjänstprenumeration`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klicka på **Generera klient** och välj Python
5. Spara klientbiblioteket

Du kan använda Python-biblioteket som du genererade med [taltjänstexemplen på GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referensdokument

* [REST (Swagger): Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)
* [REST API: Tal-till-text](rest-speech-to-text.md)
* [REST API: Text-till-tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Exempel på taltjänst på GitHub](https://aka.ms/csspeech/samples).
* [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
