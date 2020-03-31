---
title: Jämför produktfunktioner i Azure Content Delivery Network (CDN) | Microsoft Docs
description: Läs mer om de funktioner som varje Azure Content Delivery Network-produkt (CDN) stöder.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 64b906c8a6b52d9c9655f3fe2b13d504d8eed4cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74278079"
---
# <a name="compare-azure-cdn-product-features"></a>Jämföra Azure CDN-produktfunktioner

Azure Content Delivery Network (CDN) innehåller fyra produkter: **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon**. Information om hur du migrerar en **Azure CDN Standard från Verizon**-profil till **Azure CDN Premium från Verizon** finns i [Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon](cdn-migrate.md). Observera att även om det finns en uppgraderingsväg från Standard Verizon till Premium Verizon, finns det ingen konverteringsmekanism mellan andra produkter just nu.

I följande tabell jämförs de funktioner som är tillgängliga med respektive produkt.

| **Prestandafunktioner och optimeringar** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dynamisk webbplatsacceleration](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Erbjuds via [Azure Ytterdörrstjänst](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamisk platsacceleration - adaptiv bildkomprimering](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamisk platsacceleration - objektförfetch](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Allmän optimering av webbleverans](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** väljer du den här optimeringstypen om den genomsnittliga filstorleken är mindre än 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimering av videoströmning](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via allmän webbleverans | **&#x2713;**  | via allmän webbleverans |  via allmän webbleverans |
| [Optimering av stora filer](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via allmän webbleverans | **&#x2713;** väljer du den här optimeringstypen om den genomsnittliga filstorleken är större än 10 MB   | via allmän webbleverans |  via allmän webbleverans |
| Ändra optimeringstyp | |**&#x2713;** | | |
| Ursprung Port |Alla TCP-portar |[Tillåtna ursprungsportar](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alla TCP-portar |Alla TCP-portar |
| [Global serverbelastningsutjämning (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snabbrensning](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Rensa alla och Jokerteckenrensning stöds inte av Azure CDN från Akamai för närvarande |**&#x2713;** |**&#x2713;** |
| [Inläsning av tillgångar i förväg](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Inställningar för cache/huvud (med hjälp av [cachelagringsregler](cdn-caching-rules.md))  |**&#x2713;** med hjälp av [standardregler motor](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Anpassningsbar, regelbaserad innehållsleveransmotor |**&#x2713;** med hjälp av [standardregler motor](cdn-standard-rules-engine.md)  | | |**&#x2713;** med hjälp av [regelmotor](cdn-rules-engine.md) |
| Inställningar för cache/huvud  |**&#x2713;** med hjälp av [standardregler motor](cdn-standard-rules-engine.md) | | |**&#x2713;** med hjälp av [Premium regler motor](cdn-rules-engine.md) |
| URL-omdirigering/skriva om |**&#x2713;** med hjälp av [standardregler motor](cdn-standard-rules-engine.md)  | | |**&#x2713;** med hjälp av [Premium regler motor](cdn-rules-engine.md) |
| Regler för mobila enheter  |**&#x2713;** med hjälp av [standardregler motor](cdn-standard-rules-engine.md) | | |**&#x2713;** med hjälp av [Premium regler motor](cdn-rules-engine.md) |
| [Cachelagring av frågesträngar](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Dual stack-IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Stöd för HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Säkerhet** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Stöd för HTTPS med CDN-slutpunkt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egen domän-HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, Kräver direkt CNAME för att aktivera |**&#x2713;** |**&#x2713;** |
| [Stöd för anpassade domännamn](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtrering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenautentisering](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-skydd](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ha med ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Analys och rapportering** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Core-rapporter från Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Anpassade rapporter från Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtidsstatistik](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Gränsnodsprestanda](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtidsaviseringar](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Användarvänlighet** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Enkel integrering med Azure-tjänster, bland annat [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) och [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Hantering via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)eller [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Mime-typer för komprimering](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Endast standard |Konfigurerbar |Konfigurerbar  |Konfigurerbar  |
| Komprimeringskodningar  |gzip, brotli |Gzip |gzip, tömma, bzip2, brotili  |gzip, tömma, bzip2, brotili  |






