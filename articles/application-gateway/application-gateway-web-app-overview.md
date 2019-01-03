---
title: Översikt över flera klientorganisationer som slutar med Azure Application Gateway
description: Den här sidan beskriver hur du kan använda serverdelar för flera klientorganisationer med Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/1/2018
ms.author: victorh
ms.openlocfilehash: 8e5f48e42a2a677622dae0b733f9d5af484d1c51
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605814"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Stöd för serverdelar för flera klientorganisationer i Application Gateway

Azure Application Gateway stöder skalningsuppsättningar för virtuella datorer, nätverksgränssnitt, offentliga och privata IP-adresser och fullständigt kvalificerade namn (FQDN) som en del av dess serverdelspooler. Som standard ändrar inte Application Gateway den inkommande HTTP-rubriken från klienten. Rubriken skickas oförändrad till servern. Det finns många tjänster, t.ex [Azure App Service](../app-service/overview.md) som är flera klientorganisationer och förlitar sig på en specifik värdrubrik eller ett SNI-tillägg för att matcha mot rätt slutpunkt. Application Gateway stöder nu möjligheten för användare att skriva över den inkommande HTTP-Värdrubriken baserat på backend-HTTP-inställningarna. Detta gör det möjligt att använda API-hantering och Azure-webbappar med serverdelar för flera klientorganisationer. Den här funktionen är tillgänglig för både standard-SKU och WAF-SKU. Stöd för flera klienter backend-fungerar även med SSL-avslutning och SSL-scenarier från slutpunkt till slutpunkt.

> [!NOTE]
> Ställa in autentisering servercertifikat krävs inte för betrodda Azure-tjänster som Azure Web Apps.

![Scenario för webbappar](./media/application-gateway-web-app-overview/scenario.png)

Möjligheten att värdåsidosättningar definieras i HTTP-inställningarna och kan tillämpas på valfri serverdelspool samband med regelgenereringen. En värdrubrik eller ett SNI-tillägg kan åsidosättas på följande två sätt på serverdelar för flera klientorganisationer.

1. Värdnamnet kan anges till ett fast värde i HTTP-inställningarna. Den här funktionen ser till att värdhuvudet åsidosätts till det här värdet för all trafik till backend-poolen där HTTP-inställningarna tillämpas. När du använder SSL för slutpunkt till slutpunkt används det åsidosatta värdnamnet i SNI-tillägget. Den här funktionen möjliggör scenarier där en backend-poolen servergrupp förväntar sig en värdrubrik som skiljer sig från den inkommande klientvärdrubriken.

2. Möjligheten att härleda värdnamnet från IP-Adressen eller FQDN för backend-poolmedlemmar. HTTP-inställningarna innehåller också ett alternativ för att hämta värdnamnet från medlem backend-poolen FQDN om har konfigurerats med alternativet för att härleda värdnamnet från en medlem i den enskilda backend-poolen. När SSL för slutpunkt till slutpunkt används härleds det här värdnamnet från det fullständigt kvalificerade domännamnet och används i SNI-tillägget. Den här funktionen möjliggör scenarier där en serverdelspool kan ha två eller flera flera innehavare PaaS-tjänster som Azure-webbappar och varje medlem i begäran värdhuvudet innehåller värdnamn som härletts från dess FQDN.

> [!NOTE]
> I båda fallen ovan påverkar inställningarna endast livetrafikens beteende, inte beteendet vid hälsoavsökningar. Anpassade avsökningar stöder redan möjligheten att ange en värdrubrik i avsökningskonfigurationen. Anpassade avsökningar stöder nu även möjligheten att härleda värdrubrikens beteende från de konfigurerade HTTP-inställningarna. Den här konfigurationen kan anges med hjälp av `PickHostNameFromBackendHttpSettings`-parametern i avsökningskonfigurationen. För att funktionen för slutpunkt till slutpunkt ska fungera måste både avsöknings- och HTTP-inställningarna ändras så att de återspeglar korrekt konfiguration.

Med den här funktionen skapar kunderna lämplig konfiguration genom att konfigurera alternativen i HTTP-inställningarna och de anpassade avsökningarna. Den här inställningen kopplas sedan till en lyssnare och en backend-poolen med hjälp av en regel.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en Programgateway med en webbapp som medlem backend-poolen genom att gå till: [Konfigurera App Service-webbappar med Application Gateway](application-gateway-web-app-powershell.md)
