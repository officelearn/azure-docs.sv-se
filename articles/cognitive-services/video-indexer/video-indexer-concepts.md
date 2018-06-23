---
title: Azure Video indexeraren begrepp | Microsoft Docs
description: Det här avsnittet beskrivs några begrepp för tjänsten Video indexeraren.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/17/2017
ms.author: juliako
ms.openlocfilehash: 01d92a6b55d2fb2c09cee333f482d79d2cdf763c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354573"
---
# <a name="video-indexer-concepts"></a>Video indexeraren begrepp
 
Det här avsnittet beskrivs några begrepp för tjänsten Video indexeraren.
    
## <a name="summarized-insights"></a>Sammanställd insikter

Sammanställd insikter innehåller en samlad vy över data: ytor, nyckelord, våra. Till exempel i stället för att gå över var och en av tusentals tidsintervall och kontrollerar vilken ytor i den sammanfattande insikter innehåller alla ytor och för var och en, tidsintervall som det visas i och % av tiden det visas.

## <a name="topicskeywords"></a>Avsnitt om/nyckelord

Avsnitt/nyckelord finns i listan med viktiga fraser som Video indexeraren extraheras från texten. Till exempel en Scott Guthrie video kan innehålla följande avsnitt/nyckelord: säkerhet, Azure, Microsoft Cloud, intäkter.

## <a name="sentiments"></a>Våra

När Video indexeraren analyserar betyg, identifierar våra samt. Till exempel är ”är en mycket spännande händelse” ett positivt sentiment.

## <a name="time-range-vs-adjusted-time-range"></a>tidsintervall kontra justerade tidsintervall

TimeRange är tidsintervallet i den ursprungliga videon. AdjustedTimeRange är tidsintervallet i förhållande till aktuell spelningslista. Eftersom du kan skapa en spellista från olika rader med olika videor måste du ta en video 1 timme och använder bara 1 rad från den, till exempel 10:00 – 10:15. I så fall måste en spellista med 1 rad där tidsintervallet 10:00 – 10:15 men adjustedTimeRange är 00:00-00:15.
 
## <a name="blocks"></a>Block

Block är avsedda att göra det lättare att gå igenom informationen. Till exempel kan block delas upp baserat på när högtalare ändrar eller det finns en paus.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns [hur du registrerar dig och skicka videon första](video-indexer-get-started.md).

## <a name="see-also"></a>Se också

[Indexeraren videoöversikt](video-indexer-overview.md)

