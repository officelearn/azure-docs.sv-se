---
title: Känna igen tal med hjälp av REST-API
description: Lär dig hur du använder tal till Text i Speech-tjänsten
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 24fa3882a65bf6605444a139ad5d4ee42800a8ef
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42744937"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Känna igen tal med hjälp av REST-API

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

REST API kan användas för att identifiera kort yttranden med hjälp av en HTTP POST-begäran.

REST-API: et är det enklaste sättet att känna igen tal om du inte använder ett språk som stöds av den [SDK](speech-sdk.md).
Du kan göra en HTTP POST begäran till tjänsteslutpunkt, överför hela uttryck i brödtexten i begäran. och du får ett svar som innehåller den tolkade texten.

> [!NOTE]
> Yttranden är begränsade till 15 sekunder eller mindre när du använder REST-API.
> Kolla in den [tal SDK](how-to-recognize-speech-csharp.md) för igenkänning av längre yttranden.

Mer information om den **tal till Text** REST API, se [REST API: er](rest-apis.md#speech-to-text). Om du vill se hur det fungerar i praktiken kan du ladda ned den [REST API-exempel](https://github.com/Azure-Samples/SpeechToText-REST) från GitHub.

## <a name="next-steps"></a>Nästa steg

- [Se en REST API-översikt](rest-apis.md)
