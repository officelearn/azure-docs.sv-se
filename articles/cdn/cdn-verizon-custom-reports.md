---
title: "Anpassade rapporter från Verizon | Microsoft Docs"
description: "Du kan visa användningsmönster för din CDN med hjälp av följande rapporter: bandbredd, överförda Data, träffar, Cache status, Cache träffar förhållandet, IPV4/IPv6-Data överförs."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: 8df9fd46fe3ce8d87e7ad5377a21a0bc7a458d2b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="custom-reports-from-verizon"></a>Anpassade rapporter från Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Du kan definiera vilken typ av data ska samlas in för edge CNAME-resursposter rapporter med hjälp av Verizon egna rapporter via hantera portal för Verizon profiler.


## <a name="accessing-verizon-custom-reports"></a>Åtkomst till Verizon egna rapporter
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och klicka sedan hovra över den **egna rapporter** utfällbar. Klicka på **kant CNAME-resursposter**.
   
    ![CDN-hanteringsportalen - anpassade rapporter menyn](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Edge CNAME-RESURSPOSTER anpassad rapport
Edge CNAME-RESURSPOSTER anpassade rapporten ger träffar och data som överförts statistik för edge CNAME-resursposter som anpassad rapport loggning har aktiverats. Edge CNAME-resursposter består av Azure CDN-slutpunkten värdnamn och alla associerade domänen värdnamn. 

Anpassad rapport dataloggning börjar en timme efter att du aktiverar en kant CNAME anpassade rapporteringsfunktion. Du kan visa rapportdata genom att generera en rapport för Edge skapa CNAME-poster för en viss plattform eller för alla plattformar. Täckning för den här rapporten är begränsad till edge CNAME-resursposter som anpassad rapportdata har samlats in under den angivna tidsperioden. Edge CNAME-resursposter rapporten består av ett diagram och en datatabell för 10 överkant CNAME-resursposter enligt mått som definieras i alternativet mått. 

Skapa en anpassad rapport genom att definiera följande rapportalternativ:

- Mått: Stöds följande alternativ:

   - Träffar: Anger det totala antalet begäranden som dirigeras till en kant CNAME-post som den anpassade rapporteringsfunktion är aktiverad. Det här måttet inkluderar inte Statuskoden returneras till klienten.

   - Data överförda: Anger den totala mängden data som överförs från kant-servrar till HTTP-klienter (till exempel webbläsare) för begäranden som dirigeras till en kant CNAME-post som den anpassade rapporteringsfunktion är aktiverad. Mängden data som överförs beräknas genom att lägga till HTTP-svarshuvuden brödtext för svar. Därför är mängden data som överförs för varje tillgång större än den faktiska filstorleken.

- Grupperingar: Anger typ av statistik som visas nedan stapeldiagrammet. Stöd för följande alternativ:

   - HTTP-svarskoder: Ordnar statistik av HTTP-svarskoden (t.ex, 200, 403, osv) returneras till klienten. 

   - Cachestatus: Organiserar statistik efter Cachestatus för.


Om du vill ange datumintervall för rapporten, du kan antingen välja ett fördefinierat datumintervall som **idag** eller **veckans**, från den nedrullningsbara listan eller du kan välja **anpassade** och Ange ett datumintervall manuellt genom att klicka på kalenderikonerna. 

När du har valt datumintervallet, klickar du på **Gå** att generera rapporten.

Du kan exportera data i Excel-format genom att klicka på symbolen Excel till höger om den **Gå** knappen.

![Skapa CNAME-poster rapport](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAME-RESURSPOSTER anpassade fält

| Fält                     | Beskrivning   |
|---------------------------|---------------|
| 2xx                       | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en 2xx HTTP-statuskoden (t.ex, 200 OK). |
| 3xx                       | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en 3xx HTTP-statuskoden (till exempel 302 hittades eller 304 inte har ändrats. |
| 4xx                       | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en 4xx HTTP-statuskoden (till exempel 400 Felaktig begäran, 403 otillåtna eller 404 hittades). |
| 5xx                       | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i ett 5xx HTTP-statuskoden (till exempel 500 Internt serverfel eller 502 felaktig Gateway). |
| Antal träffar i %               | Anger procentandelen Cacheable ställs begäranden som har hanteras direkt från cache till beställaren. |
| Träffar i cache                | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en cache-träff (till exempel TCP_EXPIRED_HIT, TCP_HIT eller TCP_PARTIAL_HIT). En cache-träff inträffar när en cachelagrad version av det begärda innehållet finns. |
| Överförda data (MB)     | Anger den totala mängden data som överförs (MB) från kant-servrar till http-klienter (webbläsare) för kvalificerade CNAME-post. Mängden data som överförs beräknas genom att lägga till HTTP-svarshuvuden brödtext för svar. Därför är mängden data som överförs för varje tillgång större än den faktiska filstorleken. |
| Beskrivning               | Identifierar en kant CNAME av dess värdnamn |
| Träffar                      | Anger det totala antalet begäranden till gränsen CNAME |
| Missar                    | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en cache-miss (till exempel TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS eller TCP_MISS). En cache-miss inträffar när det begärda innehållet inte har cachelagrats på edge-server som hanteras av begäran. | 
| Ingen Cache                  | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en CONFIG_NOCACHE cache-statuskod.  |
| Annat                     | Anger det totala antalet begäranden eller data överförda (MB) för kvalificerade CNAME anges som resulterar i en HTTP-statuskod som faller utanför 2xx – 5xx intervall. |
| Plattform                  | Anger den plattform som hanterar edge CNAME-trafik. |
| Otilldelade               | Anger det totala antalet förfrågningar eller data som överförs (MB) för edge CNAME för vilka cache statuskod eller HTTP-statuskod information inte har loggats.  |
| Uncacheable               | Anger det totala antalet förfrågningar eller data som överförs (MB) för kvalificerade CNAME som resulterar i en UNCACHEABLE cache-statuskod.  |


## <a name="considerations"></a>Överväganden
Rapporter kan genereras bara under de senaste 18 månaderna.

