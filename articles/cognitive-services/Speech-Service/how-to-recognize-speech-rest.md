---
title: Känna igen tal med hjälp av REST-API
description: Lär dig hur du använder tal till Text-API i Cognitive Services tal-tjänst.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: 4e87feae7e3321efe7adcdbf1302762f539f034d
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167281"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Känna igen tal med hjälp av REST-API

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

REST API kan användas för att identifiera kort yttranden med hjälp av en HTTP POST-begäran.

REST-API: et är det enklaste sättet att känna igen tal om du inte använder ett språk som stöds av den [SDK](speech-sdk.md). Du gör en HTTP POST-begäran till tjänstslutpunkten och skickar hela uttryck i brödtexten i begäran. Du får ett svar som har den tolkade texten.

> [!NOTE]
> Yttranden är begränsade till 15 sekunder eller mindre när du använder REST-API.
> Kolla in den [tal SDK](how-to-recognize-speech-csharp.md) för igenkänning av längre yttranden.

Mer information om den **tal till Text** REST API kan du läsa den [REST API: er](rest-apis.md#speech-to-text) artikeln. Visa API: et i praktiken genom att ladda ned den [REST API-exempel](https://github.com/Azure-Samples/SpeechToText-REST) från GitHub.

## <a name="next-steps"></a>Nästa steg

- Se den [REST API-översikt](rest-apis.md).
