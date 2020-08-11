---
title: Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: Tal-CLI är ett kommando rads verktyg för att använda tal tjänsten utan att skriva någon kod. Tal-CLI kräver minimal konfiguration och det är enkelt att börja experimentera med viktiga funktioner i tal tjänsten för att se om dina användnings fall kan uppfyllas.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067434"
---
# <a name="what-is-the-speech-cli"></a>Vad är Speech CLI?

Tal-CLI är ett kommando rads verktyg för att använda tal tjänsten utan att skriva någon kod. Tal CLI kräver minimal installation, och det är enkelt att börja experimentera med viktiga funktioner i tal tjänsten för att se om dina användnings fall kan uppfyllas. Inom några minuter kan du köra enkla test arbets flöden som batch tal igenkänning från en katalog med filer eller text till tal i en samling med strängar från en fil. I många enkla arbets flöden är tal CLI produktions klart och kan skalas upp för att köra större processer med hjälp av automatiserade `.bat` eller Shell-skript.

Majoriteten av de viktigaste funktionerna i talet SDK är tillgängliga i tal-CLI och vissa avancerade funktioner och anpassningar är förenklade i tal CLI. Överväg följande rikt linjer för att bestämma när du ska använda tal-CLI eller tal-SDK.

Använd tal-CLI när:
* Du vill experimentera med tal tjänst funktioner med minimal konfiguration och ingen kod
* Du har relativt enkla krav för ett produktions program med hjälp av tal tjänsten

Använd tal-SDK: n när:
* Du vill integrera funktionen för tal tjänster på ett speciellt språk eller en plattform (t. ex. C#, python, C++)
* Du har komplexa krav som kan kräva avancerade tjänst begär Anden eller utveckla anpassade beteenden inklusive svars strömning

## <a name="core-features"></a>Kärn funktioner

* Tal igenkänning – konvertera tal till text från ljudfiler eller direkt från en mikrofon eller genom att skriva över en inspelad konversation.

* Tal syntes – konvertera text till tal med inmatade text från textfiler eller mata in direkt från kommando raden. Anpassa tal utmatnings egenskaper med [SSML-konfigurationer](speech-synthesis-markup.md)och antingen [standard-eller neurala-röster](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tal översättning – Översätt ljud på ett käll språk till text eller ljud på ett mål språk.

* Kör på Azure Compute Resources – skicka tal CLI-kommandon som ska köras på en Azure Remote Compute-resurs med hjälp av `spx webjob` .

## <a name="get-started"></a>Kom igång

Information om hur du kommer igång med tal-CLI finns i [artikeln Basics](spx-basics.md). Den här artikeln visar hur du kör några grundläggande kommandon och visar även något mer avancerade kommandon för att köra batch-åtgärder för tal-till-text och text till tal. När du har läst grunderna i artikeln bör du ha tillräckligt med en förståelse för syntaxen för att börja skriva vissa anpassade kommandon eller automatisera enkla tal tjänst åtgärder.

## <a name="next-steps"></a>Nästa steg

- [Grunderna för tal-CLI](spx-basics.md)
- Om din användnings fråga är mer komplex [hämtar du talet SDK](speech-sdk.md)
