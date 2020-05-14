---
title: SPX-tal-tjänst
titleSuffix: Azure Cognitive Services
description: SPX är ett kommando rads verktyg för att använda tal tjänsten utan att skriva någon kod. SPX kräver minimal konfiguration och det är enkelt att börja experimentera med viktiga funktioner i tal tjänsten för att se om dina användnings fall kan uppfyllas.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: b473bdc516c59b55eeb44f227352497142a4383b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202286"
---
# <a name="what-is-spx"></a>Vad är SPX?

SPX är ett kommando rads verktyg för att använda tal tjänsten utan att skriva någon kod. SPX kräver minimal installation, och det är enkelt att börja experimentera med viktiga funktioner i tal tjänsten för att se om dina användnings fall kan uppfyllas. Inom några minuter kan du köra enkla test arbets flöden som batch tal igenkänning från en katalog med filer eller text till tal i en samling med strängar från en fil. Utöver enkla arbets flöden är SPX produktions klart och kan skalas upp för att köra större processer med hjälp av automatiserade `.bat` eller Shell-skript.

Majoriteten av de viktigaste funktionerna i talet SDK är tillgängliga i SPX, men vissa avancerade funktioner och anpassningar är förenklade i SPX. Överväg följande rikt linjer för att bestämma när du ska använda SPX eller SDK.

Använd SPX när:
* Du vill experimentera med tal tjänst funktioner med minimal konfiguration och ingen kod
* Du har relativt enkla krav för ett produktions program med hjälp av tal tjänsten

Använd SDK: n när:
* Du vill integrera funktionen för tal tjänster på ett speciellt språk eller en plattform (t. ex. C#, python, C++)
* Du har komplexa krav som kan kräva avancerade tjänst begär Anden eller utveckla anpassade beteenden inklusive svars strömning

## <a name="core-features"></a>Kärn funktioner

* Tal igenkänning – konvertera tal till text från ljudfiler eller direkt från en mikrofon eller genom att skriva över en inspelad konversation.

* Tal syntes – konvertera text till tal med inmatade text från textfiler eller mata in direkt från kommando raden. Anpassa tal utmatnings egenskaper med [SSML-konfigurationer](speech-synthesis-markup.md)och antingen [standard-eller neurala-röster](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tal översättning – Översätt ljud på ett käll språk till text på ett mål språk.

* Kör på Azure Compute Resources – skicka SPX-kommandon som ska köras på en Azure-fjärrberäknings resurs med hjälp av `spx webjob` .

## <a name="get-started"></a>Kom igång

Information om hur du kommer igång med SPX finns i [artikeln Basics](spx-basics.md). Den här artikeln visar hur du kör några grundläggande kommandon i SPX, och visar även något mer avancerade kommandon för att köra batch-åtgärder för tal-till-text och text till tal. När du har läst grunderna i artikeln bör du ha tillräckligt med en förståelse av SPX-syntaxen för att börja skriva vissa anpassade kommandon eller automatisera enkla tal åtgärder.

## <a name="next-steps"></a>Nästa steg

- [Grunderna i SPX](spx-basics.md)
- Om din användnings fråga är mer komplex [hämtar du talet SDK](speech-sdk.md)
