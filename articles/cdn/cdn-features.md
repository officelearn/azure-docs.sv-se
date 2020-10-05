---
title: Jämför produktfunktioner i Azure Content Delivery Network (CDN) | Microsoft Docs
description: Läs mer om de funktioner som varje Azure Content Delivery Network-produkt (CDN) stöder.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0e57ae691bf4b07b8161bc343929510d6be041a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "81260521"
---
# <a name="compare-azure-cdn-product-features"></a>Jämföra Azure CDN-produktfunktioner

Azure Content Delivery Network (CDN) innehåller fyra produkter: **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon**. Information om hur du migrerar en **Azure CDN Standard från Verizon**-profil till **Azure CDN Premium från Verizon** finns i [Migrera en Azure CDN-profil från Standard Verizon till Premium Verizon](cdn-migrate.md). Observera att det finns en uppgraderings väg från standard Verizon till Premium Verizon, men det finns ingen konverterings funktion mellan andra produkter för tillfället.

I följande tabell jämförs de funktioner som är tillgängliga med respektive produkt.

| **Prestanda funktioner och optimeringar** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Acceleration av dynamisk webbplats](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Erbjuds via [Azures tjänst för front dörr](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Acceleration av dynamisk webbplats – adaptiv bild komprimering](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Acceleration av dynamisk webbplats – för hämtning av objekt](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Allmän optimering av webb leverans](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** väljer du den här optimerings typen om den genomsnittliga fil storleken är mindre än 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimering av videoströmning](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via allmän webb leverans | **&#x2713;**  | via allmän webb leverans |  via allmän webb leverans |
| [Optimering av stora filer](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via allmän webb leverans | **&#x2713;** väljer du den här optimerings typen om den genomsnittliga fil storleken är större än 10 MB   | via allmän webb leverans |  via allmän webb leverans |
| Typ av ändrings optimering | |**&#x2713;** | | |
| Ursprungs port |Alla TCP-portar |[Tillåtna ursprungs portar](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alla TCP-portar |Alla TCP-portar |
| [Global serverbelastningsutjämning (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snabbrensning](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** rensa alla och jokertecken stöds inte av Azure CDN från Akamai för närvarande |**&#x2713;** |**&#x2713;** |
| [Inläsning av tillgångar i förväg](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Inställningar för cache/huvud (med hjälp av [cachelagringsregler](cdn-caching-rules.md))  |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Anpassningsbar, regel baserad innehålls leverans motor |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md)  | | |**&#x2713;** med hjälp av [regel motor](cdn-rules-engine.md) |
| Inställningar för cache/huvud  |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md) | | |**&#x2713;** med hjälp av [Premium Rules Engine](cdn-rules-engine.md) |
| URL-omdirigering/omskrivning |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md)  | | |**&#x2713;** med hjälp av [Premium Rules Engine](cdn-rules-engine.md) |
| Regler för mobila enheter  |**&#x2713;** med [standard regel motor](cdn-standard-rules-engine.md) | | |**&#x2713;** med hjälp av [Premium Rules Engine](cdn-rules-engine.md) |
| [Cachelagring av frågesträngar](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Dual stack-IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Stöd för HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Säkerhet** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Stöd för HTTPS med CDN-slutpunkt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egen domän-HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** kräver direkt CNAME för att aktivera |**&#x2713;** |**&#x2713;** |
| [Stöd för anpassade domännamn](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtrering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenautentisering](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS skydd](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ha med ditt eget certifikat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| TLS-versioner som stöds | TLS 1,2, TLS 1.0/1.1- [konfigurerbart](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analyser och rapporter** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Kärn rapporter från Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Anpassade rapporter från Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtidsstatistik](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Gränsnodsprestanda](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtidsaviseringar](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Enkel att använda** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Enkel integrering med Azure-tjänster, bland annat [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) och [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Hantering via [REST API](/rest/api/cdn/), [.net](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)eller [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Komprimering av MIME-typer](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Endast standard |Konfigurerbar |Konfigurerbar  |Konfigurerbar  |
| Komprimerings kodning  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






