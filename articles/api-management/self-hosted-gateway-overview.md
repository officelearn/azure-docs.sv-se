---
title: Översikt över Azure API Management Gateway med egen värd | Microsoft Docs
description: 'Lär dig hur Azure API Management Gateway med egen värd hjälper organisationer att hantera API: er i hybrid miljöer och moln miljöer.'
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73513723"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Översikt över API Management Gateway med egen värd

I den här artikeln förklaras hur en lokal gateway-funktion möjliggör hybrid-och API-hantering i flera moln, presenterar den övergripande arkitekturen och markerar de grundläggande funktionerna.

> [!NOTE]
> Funktionen för lokal gateway är i för hands version. Under för hands versionen är den egna värdbaserade gatewayen bara tillgänglig på utvecklings-och Premium-nivåerna utan extra kostnad. Developer-nivån är begränsad till en enda lokal gateway-distribution.

## <a name="hybrid-and-multi-cloud-api-management"></a>API Management för Hybrid och flera moln

Den lokala gateway-funktionen utökar API Management stöd för miljöer med hybrid miljöer och flera moln och gör det möjligt för organisationer att effektivt och säkert hantera API: er som finns lokalt och över moln från en enda API Management tjänst i Azure.

Med den egen värdbaserade gatewayen har kunderna möjlighet att distribuera en behållar version av API Management Gateway-komponenten till samma miljöer där de är värdar för API: erna. Alla egna gatewayer hanteras från den API Management tjänsten de är federerade med, och ger kunderna insyn och enhetlig hanterings upplevelse i alla interna och externa API: er. Att placera gatewayerna nära API: erna gör det möjligt för kunder att optimera API-trafikflöden och kraven på säkerhets-och efterlevnad.

Varje API Management tjänst består av följande viktiga komponenter:

-   Hanterings plan, som visas som ett API, som används för att konfigurera tjänsten via Azure Portal, PowerShell och andra mekanismer som stöds.
-   Gateway (eller data plan) är ansvarig för proxy-API-begäranden, tillämpa principer och samla in telemetri
-   Developer-portalen som används av utvecklare för att identifiera, lära sig och publicera för att använda API: erna

Som standard distribueras alla dessa komponenter i Azure, vilket ger all API-trafik (visas som heldragna svarta pilar på bilden nedan) för att flöda genom Azure, oavsett var Server delar som implementerar API: erna finns. Den drift enkelhet som används för den här modellen kommer till kostnaden för ökad latens, problem med efterlevnad och i vissa fall ytterligare avgifter för data överföring.

![API-trafikflöde utan egen värd-Gateway](media/self-hosted-gateway-overview/without-gateways.png)

Att distribuera lokala gateways i samma miljöer som backend API-implementeringar och lägga till dem i API Management tjänsten tillåter att API-trafik flödar direkt till Server dels-API: er, vilket förbättrar svars tiden, optimerar kostnaderna för data överföring och aktiverar efterlevnad samtidigt som du behåller fördelarna med att ha en enda hanterings plats och identifiering av alla API: er i organisationen oavsett var de finns

![API-trafikflöde med egen värd-Gateway](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Paketering och funktioner

Den egen värdbaserade gatewayen är en behållare med en funktions motsvarighet som är en del av den hanterade gateway som distribueras till Azure som en del av varje API Management tjänst. Den egna värdbaserade gatewayen är tillgänglig som en Linux-baserad Docker-behållare från Microsoft Container Registry. Den kan distribueras till Docker, Kubernetes eller någon annan lösning för behållar dirigering som körs på ett skriv bord, ett Server kluster eller en moln infrastruktur.

> [!IMPORTANT]
> Vissa funktioner som är tillgängliga i den hanterade gatewayen är ännu inte tillgängliga i för hands versionen. Främst: logga till Event Hub-principen, Service Fabric integration, underordnad HTTP/2. Det finns ingen plan för att göra ett inbyggt cacheminne tillgängligt i den egna gatewayen.

## <a name="connectivity-to-azure"></a>Anslutning till Azure

Den lokala gatewayen kräver utgående TCP/IP-anslutning till Azure på port 443. Varje lokal gateway måste associeras med en enda API Management tjänst och konfigureras via dess hanterings plan. Den egna värdbaserade gatewayen använder anslutning till Azure för:

-   Rapportera status genom att skicka pulsslags meddelanden varje minut
-   Söker regelbundet efter (var tionde sekund) och tillämpar konfigurations uppdateringar när de är tillgängliga
-   Skicka begär ande loggar och mått till Azure Monitor, om det är konfigurerat att göra det
-   Skicka händelser till Application Insights, om det är inställt på detta

När anslutningen till Azure förloras kan inte gatewayen för egen värd ta emot konfigurations uppdateringar, rapportera statusen eller överföra telemetri.

Den egen värdbaserade gatewayen är utformad för att "misslyckad statisk" och kan överleva tillfälligt förlorad anslutning till Azure. Den kan distribueras med eller utan säkerhets kopiering med lokal konfiguration aktive rad. I det förra fallet sparar lokala gatewayer regelbundet en säkerhets kopia av konfigurationen på en beständig volym som är ansluten till behållaren eller pod.

När säkerhets kopiering av konfigurationen är avstängd och anslutningen till Azure avbryts:

-   Lokal gatewayer som kör fortsätter att fungera med en minnes kopia av konfigurationen
-   Stoppade egna gatewayer kommer inte att kunna starta

När säkerhets kopiering av konfigurationen är aktive rad och anslutningen till Azure avbryts:

-   Lokal gatewayer som kör fortsätter att fungera med en minnes kopia av konfigurationen
-   Stoppade egna gateways kommer att börja använda en säkerhets kopia av konfigurationen

När anslutningen återställs återansluter varje lokal gateway som påverkas av avbrottet automatiskt till den associerade API Management tjänsten och hämtar alla konfigurations uppdateringar som uppstod när gatewayen var offline.

## <a name="next-steps"></a>Nästa steg

-   [Läs ett whitepaper om du vill ha mer bakgrund i det här avsnittet](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Distribuera egen värd-Gateway till Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Distribuera egen värd-Gateway till Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
