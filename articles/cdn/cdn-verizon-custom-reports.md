---
title: Anpassade rapporter från Verizon | Microsoft-dokument
description: 'Du kan visa användningsmönster för CDN med hjälp av följande rapporter: Bandbredd, Överförda data, Träffar, Cachestatus, Cache träffförhållande, IPV4/IPV6-data som överförts.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 15f17ac6556c4ff731372dc7f738d0f58bdc3e31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593301"
---
# <a name="custom-reports-from-verizon"></a>Anpassade rapporter från Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Genom att använda Verizon Custom Reports via hantera portalen för Verizon-profiler kan du definiera vilken typ av data som ska samlas in för edge CNAMEs-rapporter.


## <a name="accessing-verizon-custom-reports"></a>Komma åt anpassade rapporter för Verizon
1. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profil](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över fliken **Analytics** och hovra sedan över det utfällbara **resultatet av anpassade rapporter.** Klicka på **Edge CNAMEs**.
   
    ![CDN-hanteringsportal - Menyn Anpassade rapporter](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Anpassad rapport om Edge CNAMES
Anpassad edge-CNAMES-rapport innehåller träffar och data överförd statistik för kant-CNAMEs som anpassad rapportloggning har aktiverats på. Edge CNAMEs består av Azure CDN-slutpunktsvärdnamn och alla associerade anpassade domänvärdnamn. 

Anpassad rapportdataloggning börjar en timme efter att du har aktiverat en kant CNAME:s anpassade rapporteringsfunktion. Du kan visa rapportdata genom att generera en Edge CNAMEs-rapport för en viss plattform eller för alla plattformar. Täckningen för den här rapporten är begränsad till de CNAM-edge-CNAMEs för vilka anpassade rapportdata samlades in under den angivna tidsperioden. Edge CNAMEs-rapporten består av ett diagram och en datatabell för de översta 10-kant-CNAMEs enligt det mått som definierats i alternativet Mått. 

Generera en anpassad rapport genom att definiera följande rapportalternativ:

- Mått: Följande alternativ stöds:

   - Träffar: Anger det totala antalet begäranden som dirigeras till ett kant-CNAME där den anpassade rapporteringsfunktionen är aktiverad. Det här måttet innehåller inte statuskoden som returneras till klienten.

   - Överförda data: Anger den totala mängden data som överförs från kantservrarna till HTTP-klienterna (till exempel webbläsare) för begäranden som dirigeras till ett cname-till-mål för kant där den anpassade rapporteringsfunktionen är aktiverad. Mängden data som överförs beräknas genom att http-svarshuvuden läggs till i svarstexten. Därför är mängden data som överförs för varje tillgång större än dess faktiska filstorlek.

- Grupperingar: Bestämmer vilken typ av statistik som visas under stapeldiagrammet. Följande alternativ stöds:

   - HTTP-svarskoder: Organiserar statistik efter HTTP-svarskod (till exempel 200, 403, etc.) som returneras till klienten. 

   - Cachestatus: Ordnar statistik efter cachestatus.


Om du vill ange datumintervallet för rapporten kan du antingen välja ett fördefinierat datumintervall, till exempel **Idag** eller **Den här veckan**, i listrutan eller välja **Anpassad** och manuellt ange ett datumintervall genom att klicka på kalenderikonerna. 

När du har valt datumintervallet klickar du på **Gå** för att generera rapporten.

Du kan exportera data i Excel-format genom att klicka på Excel-symbolen till höger om **go-knappen.**

![CNAMEs-rapport](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Anpassade rapportfält för Edge CNAMES

| Field                     | Beskrivning   |
|---------------------------|---------------|
| 2xx (2xx)                       | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en 2xx HTTP-statuskod (till exempel 200 OK). |
| 3xx (3xx)                       | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en 3xx HTTP-statuskod (till exempel 302 Hittade eller 304 Ändras inte. |
| 4xx (på andra sätt)                       | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en 4xx HTTP-statuskod (till exempel 400 felaktig begäran, 403 Förbjuden eller 404 hittades inte). |
| 5xx                       | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en 5xx HTTP-statuskod (till exempel 500 internt serverfel eller 502 felaktig gateway). |
| Cache träff %               | Anger procentandelen cachelagbara begäranden som serverades direkt från cacheminnet till beställaren. |
| Cache träffar                | Anger det totala antalet begäranden eller data som överförs (MB) för kant-CNAME som resulterar i en cacheträff (till exempel TCP_EXPIRED_HIT, TCP_HIT eller TCP_PARTIAL_HIT). En cacheträff inträffar när en cachelagd version av det begärda innehållet hittas. |
| Överförda data (MB)     | Anger den totala mängden data som överförs (MB) från kantservrarna till HTTP-klienter (webbläsare) för kanten CNAME. Mängden data som överförs beräknas genom att http-svarshuvudena läggs till i svarstexten. Därför är mängden data som överförs för varje tillgång större än dess faktiska filstorlek. |
| Beskrivning               | Identifierar ett kant-CNAME efter värdnamn |
| Träffar                      | Anger det totala antalet begäranden till kanten CNAME |
| Missar                    | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en cachemiss (till exempel TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS eller TCP_MISS). En cachemiss inträffar när det begärda innehållet inte cachelagrades på kanten-servern som fullfäst begäran. | 
| Ingen cache                  | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en CONFIG_NOCACHE-cachestatuskod.  |
| Annat                     | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som anger att det resulterar i en HTTP-statuskod som ligger utanför intervallet 2xx - 5xx. |
| Plattform                  | Anger plattformen som hanterar kanten CNAME trafik. |
| Ej tilldelad               | Anger det totala antalet begäranden eller data som överförts (MB) för kanten CNAME för vilka cachestatuskod eller HTTP-statuskodsinformation inte loggades.  |
| Kan inteachest               | Anger det totala antalet begäranden eller data som överförts (MB) för kant-CNAME som resulterar i en uncacheable-cachestatuskod.  |


## <a name="considerations"></a>Överväganden
Rapporter kan bara genereras inom de senaste 18 månaderna.

