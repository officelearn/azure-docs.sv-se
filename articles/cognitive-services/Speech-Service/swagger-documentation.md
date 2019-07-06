---
title: Swagger-dokumentation – Speech Services
titleSuffix: Azure Cognitive Services
description: 'Swagger-dokumentationen kan användas för att automatiskt generera SDK: er för ett antal programmeringsspråk. Alla åtgärder i vår tjänst som stöds av Swagger'
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f6420ea8918816f7356e567ec48cb0f7456065e0
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604776"
---
# <a name="swagger-documentation"></a>Dokumentation om Swagger

Speech Services erbjuder en Swagger-specifikation för att interagera med en handfull REST API: er används för att importera data, skapa modeller, testa modellens Precision, skapa anpassade slutpunkter, kö avskrifter för batch och hantera prenumerationer. De flesta åtgärder som är tillgängligt via portalen för anpassat tal kan utföras via programmering med hjälp av dessa API: er.

> [!NOTE]
> Både tal till Text och text till tal-åtgärder stöds som REST API: er, som i sin tur finns dokumenterade i Swagger-specifikation.

## <a name="generating-code-from-the-swagger-specification"></a>Generera kod från Swagger-specifikation

Den [Swagger-specifikationen](https://cris.ai/swagger/ui/index) har alternativ med vilka du kan snabbt testa för olika sökvägar. Men är ibland det klokt att generera kod för alla sökvägar som skapar ett enda anrop som du kan basera framtida lösningar på-bibliotek. Låt oss ta en titt på processen för att generera ett Python-bibliotek.

Du måste du ange Swagger samma region som din Speech Service-prenumeration. Du kan bekräfta din region i Azure portal under din Speech Services-resurs. En fullständig lista över regioner som stöds finns i [regioner](regions.md).

1. Gå till https://editor.swagger.io
2. Klicka på **filen**, klicka sedan på **Import**
3. Ange det swagger-Webbadress som innehåller regionen för prenumerationen Speech Services `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klicka på **generera klienten** och välj Python
5. Spara klientbiblioteket

Du kan använda Python-biblioteket som du skapade med den [Speech Services-exempel på GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referensdokument

* [REST (Swagger): Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)
* [REST-API: Speech-to-text](rest-speech-to-text.md)
* [REST-API: Text till tal](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Speech Services-exempel på GitHub](https://aka.ms/csspeech/samples).
* [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
