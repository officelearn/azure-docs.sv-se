---
title: "Översikt över serverdelar för flera klientorganisationer med Azure Application Gateway | Microsoft Docs"
description: "Den här sidan beskriver hur du kan använda serverdelar för flera klientorganisationer med Application Gateway."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: c29ff60a50e68c75b4e8f62713d6d1fffd2123d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Stöd för serverdelar för flera klientorganisationer i Application Gateway

Azure Application Gateway stöder skalningsuppsättningar för virtuella datorer, nätverksgränssnitt, offentliga och privata IP-adresser och fullständigt kvalificerade domännamn (FQDN) som en del av dess serverdelspooler. Som standard ändrar inte Application Gateway den inkommande HTTP-rubriken från klienten. Rubriken skickas oförändrad till servern. Det finns många tjänster, t.ex. [Azure Web Apps](../app-service/app-service-web-overview.md) och [API Management](../api-management/api-management-key-concepts.md), som per definition stöder flera klientorganisationer och som kräver en specifik värdrubrik eller ett specifikt SNI-tillägg för att kunna matcha mot rätt slutpunkt. Application Gateway stöder nu möjligheten för användare att skriva över den inkommande HTTP-värdrubriken baserat på HTTP-inställningarna på serverdelen. Detta gör det möjligt att använda API-hantering och Azure-webbappar med serverdelar för flera klientorganisationer. Den här funktionen är tillgänglig för både standard-SKU och WAF-SKU. Stöd för serverdelar för flera klientorganisationer fungerar även med SSL-avslutning och SSL-scenarier från slutpunkt till slutpunkt.

![Scenario för webbappar](./media/application-gateway-web-app-overview/scenario.png)

Värdåsidosättningar kan definieras i HTTP-inställningarna och kan tillämpas på valfri serverdelspool i samband med regelgenereringen. En värdrubrik eller ett SNI-tillägg kan åsidosättas på följande två sätt på serverdelar för flera klientorganisationer.

1. Värdnamnet kan anges till ett fast värde i HTTP-inställningarna. Med den här funktionen åsidosätts värdrubriken till detta värde för all trafik till serverdelspoolen som HTTP-inställningarna har tillämpats på. När du använder SSL för slutpunkt till slutpunkt används det åsidosatta värdnamnet i SNI-tillägget. Den här funktionen möjliggör scenarier där en grupp med serverdelspooler förväntar sig en värdrubrik som skiljer sig från den inkommande klientvärdrubriken.

2. Värdnamnet kan härledas från IP-adressen eller det fullständigt kvalificerade domännamnet (FQDN) för medlemmarna i serverdelspoolen. HTTP-inställningarna innehåller också ett alternativ för att hämta värdnamnet från det fullständigt kvalificerade domännamnet (FQDN) för en medlem i serverdelspoolen om det konfigurerats med alternativet för att härleda värdnamnet från en enskild medlem i serverdelspoolen. När SSL för slutpunkt till slutpunkt används härleds det här värdnamnet från det fullständigt kvalificerade domännamnet och används i SNI-tillägget. Den här funktionen möjliggör scenarier där en serverdelspool kan ha två eller flera PaaS-tjänster för flera klientorganisationer, t.ex. Azure Web Apps, och begärans värdrubrik för varje medlem innehåller värdnamnet som härletts från dess FQDN.

> [!NOTE]
> I båda fallen ovan påverkar inställningarna endast livetrafikens beteende, inte beteendet vid hälsoavsökningar. Anpassade avsökningar stöder redan möjligheten att ange en värdrubrik i avsökningskonfigurationen. Anpassade avsökningar stöder nu även möjligheten att härleda värdrubrikens beteende från de konfigurerade HTTP-inställningarna. Den här konfigurationen kan anges med hjälp av `PickHostNameFromback endAddress`-parametern i avsökningskonfigurationen. För att funktionen för slutpunkt till slutpunkt ska fungera måste både avsöknings- och HTTP-inställningarna ändras så att de återspeglar korrekt konfiguration.

Med den här funktionen skapar kunderna lämplig konfiguration genom att konfigurera alternativen i HTTP-inställningarna och de anpassade avsökningarna. Den här inställningen kopplas sedan till en lyssnare och till en serverdelspool med hjälp av en regel.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar en programgateway med en webbapp som en medlem i en serverdelspool finns i [Configure App Service Web Apps with Application Gateway](application-gateway-web-app-powershell.md) (Konfigurera App Service Web Apps med Application Gateway)
