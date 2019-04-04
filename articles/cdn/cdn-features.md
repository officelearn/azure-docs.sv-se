---
title: Jämför produktfunktioner i Azure Content Delivery Network (CDN) | Microsoft Docs
description: Läs mer om de funktioner som varje Azure Content Delivery Network-produkt (CDN) stöder.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/25/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: d78b10edd005a593b4ebe4f34ca2280ccdfdaa04
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918917"
---
# <a name="compare-azure-cdn-product-features"></a>Jämföra Azure CDN-produktfunktioner

Azure Content Delivery Network (CDN) innehåller av fyra produkter: **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, **Azure CDN Standard från Verizon** och **Azure CDN Premium from Verizon**. Information om hur du migrerar en **Azure CDN Standard från Verizon**-profil till **Azure CDN Premium från Verizon** finns i [Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon](cdn-migrate.md).

I följande tabell jämförs de funktioner som är tillgängliga med respektive produkt.

| **Prestanda och optimering** | **Standard-Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Acceleration av dynamisk webbplats](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Erbjuds via [Azure ytterdörren Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Acceleration av dynamisk webbplats – adaptiv bildkomprimering](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Acceleration av dynamisk webbplats – förhämtning av objekt](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Allmän leveransoptimering](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Välj den här typen av slutpunktsoptimering om genomsnittliga filstorleken är mindre än 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimering av videoströmning](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via General Web Delivery | **&#x2713;**  | via General Web Delivery |  via General Web Delivery |
| [Optimering av stora filer](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via General Web Delivery | **&#x2713;**, Välj den här typen av slutpunktsoptimering om genomsnittliga filstorleken är större än 10 MB   | via General Web Delivery |  via General Web Delivery |
| Ändra typ av lagringsoptimering | |**&#x2713;** | | |
| Ursprungsport |Alla TCP-portar |[Tillåtna ursprungsportar](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alla TCP-portar |Alla TCP-portar |
| [Global serverbelastningsutjämning (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snabbrensning](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Rensa och jokerteckensrensning stöds inte av Azure CDN från Akamai för närvarande |**&#x2713;** |**&#x2713;** |
| [Inläsning av tillgångar i förväg](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Inställningar för cache/huvud (med hjälp av [cachelagringsregler](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Anpassningsbar regler baserade motor för innehållsleverans (med hjälp av [regelmotor](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Inställningar för cache/huvud (med hjälp av [regelmotor](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Omdirigering/omarbetning för URL (med hjälp av [regelmotor](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regler för mobil enhet (med hjälp av [regelmotor](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Cachelagring av frågesträngar](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Dual stack-IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Stöd för HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Säkerhet** | **Standard-Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Stöd för HTTPS med CDN-slutpunkt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egen domän-HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Stöd för anpassade domännamn](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtrering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenautentisering](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-skydd](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Hämta ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Analyser och rapporter** | **Standard-Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Core-rapporter från Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Anpassade rapporter från Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtidsstatistik](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Gränsnodsprestanda](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtidsaviseringar](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Användbarhet** | **Standard-Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Enkel integrering med Azure-tjänster, bland annat [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) och [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Hantering via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) eller [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Komprimering MIME-typer](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Endast standard |Konfigurerbara |Konfigurerbara  |Konfigurerbara  |
| Komprimering kodningar  |gzip, brotli |Gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






