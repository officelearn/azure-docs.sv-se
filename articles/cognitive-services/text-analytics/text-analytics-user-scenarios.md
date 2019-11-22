---
title: Exempel på användar scenarier för API för textanalys
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att se några vanliga scenarier för att integrera API för textanalys i dina tjänster och processer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: f11d23207a21af5c73831d0edd5ffc9eaa59c119
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280660"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exempel på användar scenarier för API för textanalys

API för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av naturligt språk över text. I den här artikeln beskrivs några exempel på användnings fall för integrering av API i dina affärs lösningar och processer. 

## <a name="analyze-survey-results"></a>Analysera undersöknings resultat

Skapa insikter från resultat från kund-och personal undersökningar genom att bearbeta de obehandlade text svaren med Attitydanalys. Sammanställ resultaten för analys, uppföljning och drivande engagemang.

![En bild som beskriver hur du utför sentiment analys av enkäter för kunder och anställda.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analysera registrerade inkommande kund samtal

Extrahera insikter från kund tjänst samtal med Text till tal, Attitydanalys och Extrahering av diskussionsämne. Visa resultaten på Power BI instrument panel eller på en portal för att bättre förstå kunder, markera kund tjänst trender och driva kund engagemang. Skicka API-begäranden som en batch för rapportering eller i real tid för ingripande. Se [exempel kod](https://github.com/rlagh2/callcenteranalytics).

![En bild som beskriver hur du automatiserar insikter från kund service samtal med sentiment-analys](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Bearbeta och kategorisera support ärenden

Använd Extrahering av diskussionsämne och enhets igenkänning för att bearbeta support förfrågningar som skickats i ostrukturerat text format. Använd de extraherade fraserna och entiteterna för att kategorisera förfrågningarna för resurs planering och trend analys.

![En bild som beskriver hur du använder extrahering av nyckel fraser och enhets igenkänning för att kategorisera incident rapporter och trender](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Övervaka din produkts sociala media-flöden

Övervaka feedback från användarnas produkter på din produkts Twitter-eller Facebook-sida. Använd data för att analysera kund sentiment mot nya produkter, extrahera viktiga fraser om funktioner och funktions förfrågningar, eller ta itu med kund klagomål när de sker. Se exemplet [Microsoft Flow Template](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![En bild som beskriver hur du övervakar din produkt och företagets feedback på sociala medier med extrahering av nyckel fraser](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)
* [Skicka en begäran till API för textanalys med hjälp av klient biblioteket](quickstarts/text-analytics-sdk.md)
