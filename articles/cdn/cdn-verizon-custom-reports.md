---
title: Anpassade rapporter från Verizon | Microsoft Docs
description: 'Du kan visa användningsmönster i CDN-nätverket med hjälp av följande rapporter: bandbredd, överförda Data, träffar, cachelagring, Cache träffar förhållande, IPV4/IPv6-Data överförs.'
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: f18b6edb900640e48e5ca87639f71cec9287af38
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159749"
---
# <a name="custom-reports-from-verizon"></a>Anpassade rapporter från Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Du kan definiera vilken typ av data ska samlas in för edge CNAME-poster rapporter med hjälp av Verizon egna rapporter via portalen hantera för Verizon-profiler.


## <a name="accessing-verizon-custom-reports"></a>Åtkomst till Verizon anpassade rapporter
1. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![Knappen för CDN-profil hantera](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och sedan hovra över den **anpassade rapporter** utfällbar meny. Klicka på **Edge-CNAME-poster**.
   
    ![CDN-hanteringsportalen - anpassade rapporter menyn](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Anpassad rapport för Edge CNAME-poster
Den anpassade rapporten Edge CNAME-poster ger träffar och överförda data statistik för edge CNAME-poster som anpassad rapport inloggning har aktiverats. CNAME-poster för Edge består av Azure CDN-slutpunkten värdnamn och alla associerade anpassad domän-värdnamn. 

Anpassad rapport dataloggning börjar en timme efter att du aktiverar en edge-CNAME anpassade rapporteringsfunktionen. Du kan visa rapportdata genom att generera en rapport för Edge CNAME-poster för en viss plattform eller för alla plattformar. Täckning för den här rapporten är begränsad till edge CNAME-poster som anpassad rapportdata samlades in under den angivna tidsperioden. Edge CNAME-poster rapporten består av ett diagram och en datatabell för 10 överkant CNAME-poster enligt de mått som definieras i alternativet mått. 

Skapa en anpassad rapport genom att definiera följande alternativ för rapporter:

- Mått: Stöds följande alternativ:

   - Träffar: Anger det totala antalet begäranden som dirigeras till en edge-CNAME där anpassade rapporteringsfunktionen är aktiverat. Det här måttet inkluderar inte statuskoden som returneras till klienten.

   - Överförda data: Anger den totala mängden data som överförs från edge-servrar till HTTP-klienter (till exempel webbläsare) för begäranden som dirigeras till en edge-CNAME där anpassade rapporteringsfunktionen är aktiverat. Mängden data som överförs beräknas genom att lägga till HTTP-svarshuvuden svarstexten. Därför är mängden data som överförs för varje tillgången större än den faktiska filstorleken.

- Grupperingar: Avgör vilken typ av statistik som visas nedan stapeldiagrammet. Följande alternativ stöds:

   - HTTP-svarskoder: Organiserar statistik av HTTP-svarskoden (till exempel 200, 403, osv.) returneras till klienten. 

   - Cachestatus: Organiserar statistik av status för cache.


Om du vill ange datumintervall för rapporten, du kan antingen välja ett fördefinierat datumintervall som **idag** eller **veckans**, från den nedrullningsbara listan eller du kan välja **anpassade** och Ange ett datumintervall manuellt genom att klicka på kalenderikonerna. 

När du har valt datumintervall, klickar du på **Gå** att generera rapporten.

Du kan exportera data i Excel-format genom att klicka på symbolen Excel till höger om den **Gå** knappen.

![CNAME-poster rapport](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAME-poster, anpassade fält

| Fält                     | Beskrivning   |
|---------------------------|---------------|
| 2xx                       | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i ett 2xx HTTP-statuskoden (till exempel 200 OK). |
| 3xx                       | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en 3xx HTTP-statuskoden (till exempel 302 hittades eller 304 ändras inte. |
| 4xx                       | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en HTTP 4xx-statuskod (till exempel 400 Felaktig begäran, 403 förbjudet eller 404 hittades inte). |
| 5xx                       | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en 5xx HTTP-statuskod (exempelvis 500 Internt serverfel eller 502 felaktig Gateway). |
| -Cacheträffar %               | Anger procentandelen komma begäranden som har hanteras direkt från cachen till frågeställaren. |
| Cacheträffar                | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en cache-träff (till exempel TCP_EXPIRED_HIT, TCP_HIT eller TCP_PARTIAL_HIT). En cache-träff inträffar när en cachelagrad version av det begärda innehållet hittas. |
| Överförda data (MB)     | Anger den totala mängden data som överförs (MB) från edge-servrar till http-klienter (webbläsare) för edge CNAME. Mängden data som överförs beräknas genom att lägga till HTTP-svarshuvuden svarstexten. Därför är mängden data som överförs för varje tillgången större än den faktiska filstorleken. |
| Beskrivning               | Identifierar en kant CNAME av dess värdnamn |
| Träffar                      | Anger det totala antalet begäranden till gränsen CNAME |
| Missar                    | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en cachemiss (till exempel TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS eller TCP_MISS). En cachemiss inträffar när det begärda innehållet inte var cachelagras på edge-servern som lösts in begäran. | 
| Ingen cachelagring                  | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en CONFIG_NOCACHE cache-statuskod.  |
| Annat                     | Anger det totala antalet begäranden eller data överförda (MB) för edge CNAME anges som resulterar i HTTP-statuskoden som faller utanför 2xx – 5xx-intervallet. |
| Plattform                  | Anger den plattform som hanterar CNAME edge-trafik. |
| Otilldelad               | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME för vilka cache-statuskod eller HTTP-statuskod information inte har loggats.  |
| Uncacheable               | Anger det totala antalet begäranden eller data som överförs (MB) för edge CNAME som resulterar i en UNCACHEABLE cache-statuskod.  |


## <a name="considerations"></a>Överväganden
Rapporter kan genereras bara under de senaste 18 månaderna.

