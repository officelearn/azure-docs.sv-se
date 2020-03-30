---
title: Översikt över Azure API Management-gateway med själv värd | Microsoft-dokument
description: Lär dig hur självvärd Azure API Management gateway hjälper organisationer att hantera API:er i hybrid- och multicloud-miljöer.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513723"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Översikt över självvärderade API Management-gateway

I den här artikeln beskrivs hur självvärdbaserad gateway-funktion möjliggör hybrid- och API-hantering med flera moln, presenterar dess arkitektur på hög nivå och belyser dess grundläggande funktioner.

> [!NOTE]
> Den självvärdbaserade gatewayfunktionen är i förhandsversion. Under förhandsversionen är den självvärdbaserade gatewayen endast tillgänglig på utvecklar- och premiumnivåerna utan extra kostnad. Utvecklarnivån är begränsad till en enda självvärd gateway-distribution.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybrid- och API-hantering med flera moln

Den självvärderade gateway-funktionen utökar API Management-stöd för hybrid- och flermolnsmiljöer och gör det möjligt för organisationer att effektivt och säkert hantera API:er som finns lokalt och över moln från en enda API Management-tjänst i Azure.

Med den självvärdbaserade gatewayen har kunderna flexibiliteten att distribuera en behållarversion av API Management gateway-komponenten till samma miljöer där de är värdar för sina API:er. Alla självvärderade gateways hanteras från API Management-tjänsten som de är federerade med, vilket ger kunderna synlighet och enhetlig hanteringsupplevelse för alla interna och externa API:er. Om du placerar gateways nära API:erna kan kunderna optimera API-trafikflöden och åtgärda säkerhets- och efterlevnadskrav.

Varje API Management-tjänst består av följande nyckelkomponenter:

-   Hanteringsplan, exponerat som ett API, som används för att konfigurera tjänsten via Azure-portalen, PowerShell och andra mekanismer som stöds.
-   Gateway (eller dataplan) ansvarar för att proxyera API-begäranden, tillämpa principer och samla in telemetri
-   Utvecklarportal som används av utvecklare för att upptäcka, lära sig och ombord för att använda API:erna

Som standard distribueras alla dessa komponenter i Azure, vilket gör att all API-trafik (visas som helsvarta pilar på bilden nedan) flödar genom Azure oavsett var serverdelar implementera API:erna finns. Den här modellens operativa enkelhet beror på bekostnad av ökad latens, efterlevnadsproblem och i vissa fall ytterligare dataöverföringsavgifter.

![API-trafikflöde utan självvärderade gateways](media/self-hosted-gateway-overview/without-gateways.png)

Om du distribuerar självvärderade gateways i samma miljöer som serverdels-API-implementeringar och lägga till dem i API Management-tjänsten kan API-trafik flöda direkt till serverdels-API:erna, vilket förbättrar svarstiden, optimerar dataöverföringskostnaderna och aktiverar samtidigt som fördelarna med att ha en enda hanteringspunkt och identifiering av alla API:er inom organisationen oavsett var deras implementeringar finns.

![API-trafikflöde med självvärderade gateways](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Förpackning och funktioner

Den självvärderade gatewayen är en behållare, funktionellt likvärdig version av den hanterade gatewayen som distribueras till Azure som en del av varje API Management-tjänst. Den självvärdbaserade gatewayen är tillgänglig som en Linux-baserad Docker-behållare från Microsoft Container Registry. Den kan distribueras till Docker, Kubernetes eller någon annan behållare orchestration lösning som körs på en stationär, server kluster eller moln infrastruktur.

> [!IMPORTANT]
> Vissa funktioner som är tillgängliga i den hanterade gatewayen är ännu inte tillgängliga i förhandsversionen. Noterbart: Logga till Event Hub-principen, Integrering av serviceinfrastruktur, nedströms HTTP/2. Det finns ingen plan för att göra en inbyggd cache tillgänglig i den självvärderade gatewayen.

## <a name="connectivity-to-azure"></a>Anslutning till Azure

Den självvärderade gatewayen kräver utgående TCP/IP-anslutning till Azure i port 443. Varje självvärd gateway måste associeras med en enda API Management-tjänst och konfigureras via dess hanteringsplan. Självvärdbaserad gateway använder anslutning till Azure för:

-   Rapportera dess status genom att skicka pulsslagsmeddelanden varje minut
-   Regelbundet söka efter (var 10:e sekund) och tillämpa konfigurationsuppdateringar när de är tillgängliga
-   Skicka begärandeloggar och mått till Azure Monitor, om de är konfigurerade för att göra det
-   Skicka händelser till Application Insights, om det är inställt på att göra det

När anslutningen till Azure går förlorad kan självvärderade gateway inte ta emot konfigurationsuppdateringar, rapportera dess status eller ladda upp telemetri.

Den självvärderade gatewayen är utformad för att "misslyckas statisk" och kan överleva den tillfälliga förlusten av anslutning till Azure. Den kan distribueras med eller utan lokal konfigurationssäkerhetskopiering aktiverad. I det tidigare fallet sparar självvärderade gateways regelbundet en säkerhetskopia av konfigurationen på en beständig volym som är kopplad till behållaren eller podden.

När säkerhetskopiering av konfigurationen är inaktiverad och anslutningen till Azure avbryts:

-   Självvärdbaserade gateways som körs fortsätter att fungera med hjälp av en minneskopia av konfigurationen
-   Stoppade självvärdbaserade gateways kan inte starta

När säkerhetskopiering av konfiguration är aktiverat och anslutningen till Azure avbryts:

-   Självvärdbaserade gateways som körs fortsätter att fungera med hjälp av en minneskopia av konfigurationen
-   Stoppade självvärdbaserade gateways börjar använda en säkerhetskopia av konfigurationen

När anslutningen återställs återansluts varje självvärd gateway som påverkas av avbrottet automatiskt till den associerade API Management-tjänsten och hämtar alla konfigurationsuppdateringar som inträffade medan gatewayen var "offline".

## <a name="next-steps"></a>Nästa steg

-   [Läs ett faktablad för ytterligare bakgrund om detta ämne](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Distribuera självvärd gateway till Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Distribuera självvärd gateway till Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
