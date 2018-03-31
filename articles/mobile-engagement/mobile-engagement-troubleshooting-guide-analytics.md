---
title: Azure Mobile Engagement felsökningsguide för - Analytics
description: Felsökning av problem med Analytics, övervakning, segmentering och instrumentpanelen i Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b19d018b83ee8b3d5848d29afff190d3dcaf3fde
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Felsökningsguide för Analytics, övervakning, segmentering och instrumentpanelen problem
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Följande är möjliga problem som kan uppstå med hur Azure Mobile Engagement samlar in information om dina program, enheter och användare.

## <a name="missingdelayed-information"></a>Information saknas/fördröjd
### <a name="issue"></a>Problem
* Visas i som visas i Analytics, segmentering eller instrumentpanelen.
* Information saknas från övervakning.
* Information saknas från Analytics, segmentering eller instrumentpanelen.
* Träffa segmentering begränsar.

### <a name="causes"></a>Orsaker
* Du kan använda Analytics-API, övervaka API och segment API för att se om några data du saknas i Användargränssnittet är synliga via API: erna.
* Om Azure Mobile Engagement SDK inte är korrekt integrerad i din app du inte visa information i Analytics, segmentering, övervakning och instrumentpaneler.
* Segment får inte vara ändras när de skapas, segment kan endast vara ”klonade” (kopieras) eller ”förstörs” (bort). Segment kan bara innehålla 10 kriterier.
* Det bästa sättet att testa information saknas från övervakning är att konfigurera en testenhet, avinstallera och installera om appen på enheten test.
* Informationen uppdateras var 24: e timme för Analytics, segmentering eller instrumentpaneler.
* Informationen i nya segment visas inte förrän 24 timmar efter att de skapas även om segmentet baseras på information som tidigare.
* Filtrering analytics-data i Gränssnittet visas alla exempel på den här typen oavsett vilken version av appen (t.ex.) ”Kraschar” filtrerade efter namn visas från version 1 och version 2 av appen).
* Hur lång tid för Analytics baseras på användarnas Enhetsinställningar datum så att en användare vars telefonen har datum som felaktigt kan visas i fel tidsperioden.
* Inga serversidan data loggas när du använder knappen till ”test” push-meddelanden, data loggas endast för verkliga push-kampanjer.

## <a name="cant-locate-items-in-ui"></a>Det går inte att hitta objekt i Användargränssnittet
### <a name="issue"></a>Problem
* Kan inte skapa segment baserat på vissa inbyggda eller anpassade appinfo tagga kriterier.
* Kan inte hitta vissa inbyggda eller anpassade appinfo tagga kriterier i Analytics, övervakning och instrumentpaneler.
* Det går inte att tolka data i Analytics, övervakning, segmentering och instrumentpaneler.

### <a name="causes"></a>Orsaker
* Vissa inbyggda objekt och app-info taggar är bara tillgängliga som push-villkor, men kanske inte läggas till i ett segment eller synliga från Analytics, övervakning eller instrumentpanelen. 
* För inbyggda artiklar och app-info taggar som inte går att lägga till ett segment, behöver du installationsprogrammet listan med målobjekt för villkoret i varje kampanj utför samma funktion som mål baserat på ett segment.
* Se snabbmenyer i avsnitten Analytics, övervakning, segmentering och instrumentpaneler för Azure Mobile Engagement gränssnitt för mer hjälp och information.

## <a name="crash-troubleshooting"></a>Krasch felsökning
### <a name="issue"></a>Problem
* Programmet kraschar som visas i Analytics, övervakning eller instrumentpanelen.

### <a name="causes"></a>Orsaker
* Om du vill felsöka se programmet kraschar ses i Analytics, övervakning eller instrumentpanelen viktig information om kända problem med tidigare versioner av SDK.
* Om du vill felsöka detta ytterligare utföra programkrascher en händelse från en testenhet med ditt program installerat och leta upp din enhets-ID i avsnittet ”övervaka – händelser” i Azure Mobile Engagement-Användargränssnittet. Utföra händelsen som gör att programmet kraschar och leta upp ytterligare information i avsnittet ”övervakaren – krascher” Azure Mobile Engagement-gränssnitt. 

