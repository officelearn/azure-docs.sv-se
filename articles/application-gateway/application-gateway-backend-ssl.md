---
title: Aktivera SSL-principen och slutpunkt till slutpunkt SSL på Application Gateway | Microsoft Docs
description: Den här sidan ger en översikt över slutpunkt-till-slutpunkt SSL-stöd för Application Gateway.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: amsriva

---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Aktivera SSL-princip och slutpunkt-till-slutpunkt-SSL på Application Gateway
## <a name="overview"></a>Översikt
Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdels-servrarna. På så sätt kan webbservrarna slippa kostsam kryptering/avkryptering. För en del kunder är dock inte okrypterad kommunikation till serverdels-servrarna en acceptabel lösning. Det kan bero på säkerhets-/efterföljandekrav eller att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

Slutpunkt-till-slutpunkt SSL låter dig säkert skicka krypterad känslig data till serverdelen samt dra nytta av de fördelar med Layer 7-belastningsutjämningsfunktioner som Application Gateway tillhandahåller, som cookie-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-vidarebefordrade-* huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Slutpunkt-till-slutpunkt SSL aktiveras genom att ställa in protokollinställningen i BackendHTTPSetting till HTTPS, som sedan appliceras till en serverdelspool. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

I det här exemplet kan begäranden för https://contoso.com dirigeras till ContosoServerPool över HTTP och https://fabrikam.com kommer att dirigeras till FabrikamServerPool över HTTPS med slutpunkt-till-slutpunkt SSL.

## <a name="end-to-end-ssl-and-white-listing-of-certificates"></a>Slutpunkt till slutpunkt SSL och vitlistning av certifikat
Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway. Endast anslutningar till kända och vitlistade serverdelar tillåts sedan och återstående resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste också vitlistas med Application Gateway som det beskrivs ovan, innan de kan användas.

## <a name="application-gateway-ssl-policy"></a>SSL-princip för Application Gateway
Application Gateway stöder också användarkonfigurerbara SSL-förhandlingsprinciper som tillåter användare störrre kontroll över SSL-anslutningar på Application Gateway.

1. SSL 2.0 och 3.0 är framtvingat inaktiverade för alla Application Gateways. De kan inte konfigureras alls.
2. SSL-principdefinitioner ger dig alternativet att inaktivera alla följande tre protokoll – TLSv1_0 TLSv1_1, TLSv1_2.
3. Om ingen SSL-princip har definierats så är alla tre (TLSv1_0 TLSv1_1, TLSv1_2) aktiverade.

## <a name="next-steps"></a>Nästa steg
När du läst om slutpunkt-till-slutpunkt SSL och SSL-principer, kan du gå till [Aktivera slutpunkt-till-slutpunkt SSL på Application Gateway](application-gateway-end-to-end-ssl-powershell.md) för att skapa en Application Gateway som kan skicka trafik till serverdelen i krypterad form.

<!--HONumber=Oct16_HO3-->


