---
title: Exempel på användarscenarier för API:et för textanalys
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill se några vanliga scenarier för att integrera API:et för textanalys i dina tjänster och processer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219251"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exempel på användarscenarier för API:et för textanalys

Api:et för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av naturligt språk via text. I den här artikeln beskrivs några exempel på användningsfall för att integrera API:et i dina affärslösningar och processer. 

## <a name="analyze-survey-results"></a>Analysera undersökningsresultat

Dra insikter från kund- och medarbetarundersökningsresultat genom att bearbeta de råa textsvaren med hjälp av sentimentanalys. Aggregera resultaten för analys, uppföljning och drivande engagemang.

![En bild som beskriver hur man utför sentimentanalys på kund- och medarbetarundersökningar.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analysera inspelade inkommande kundsamtal

Extrahera insikter från kundtjänstsamtal med hjälp av text till tal, sentimentanalys och extrahering av nyckelfraser. Visa resultaten i Power BI-instrumentpanelen eller en portal för att bättre förstå kunder, markera kundservicetrender och öka kundengagemanget. Skicka API-begäranden som en batch för rapportering eller i realtid för intervention. Se exempelkoden [på GitHub](https://github.com/rlagh2/callcenteranalytics).

![En bild som beskriver hur du automatiserar att få insikter från kundtjänstsamtal med hjälp av sentimentanalys](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Bearbeta och kategorisera supportincidenter

Använd extrahering av nyckelfraser och entitetsigenkänning för att bearbeta supportbegäranden som skickas i ostrukturerat textformat. Använd de extraherade fraserna och entiteterna för att kategorisera begäranden om resursplanering och trendanalys.

![En bild som beskriver hur du använder extrahering av nyckelfraser och entitetsigenkänning för att kategorisera incidentrapporter och trender](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Övervaka produktens flöden i sociala medier

Övervaka feedback från användarprodukter på produktens Twitter- eller Facebook-sida. Använd data för att analysera kundernas sentiment mot nya produkter lanseringar, extrahera nyckelfraser om funktioner och begäranden funktionen, eller adress kundklagomål när de inträffar. Se exemplet [Microsoft Flow-mallen](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![En bild som beskriver hur du övervakar din produkt- och företagsfeedback på sociala medier med hjälp av utvinning av nyckelfraser](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klassificera och redigera dokument som har känslig information

Använd Namngiven entitetsigenkänning för att identifiera personlig och känslig information i dokument. Använd data för att klassificera dokument eller redigera dem så att de kan delas på ett säkert sätt.

![En bild som beskriver hur du använder NER för att upptäcka personlig information och klassificera och redigera dokument](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)
* [Skicka en begäran till API:et för textanalys med hjälp av klientbiblioteket](quickstarts/text-analytics-sdk.md)
