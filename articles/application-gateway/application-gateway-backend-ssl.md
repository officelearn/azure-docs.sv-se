---
title: "Aktivera SSL-principen och slutpunkt till slutpunkt SSL på Application Gateway | Microsoft Docs"
description: "Den här sidan ger en översikt över slutpunkt-till-slutpunkt SSL-stöd för Application Gateway."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: 40368e31790a7ffa2d34a51a13e78d028cd0a1eb
ms.lasthandoff: 04/05/2017


---
# <a name="overview-of-end-to-end-ssl-and-ssl-policy-on-application-gateway"></a>Översikt över slutpunkt-till-slutpunkt-SSL och SSL-princip på Application Gateway

Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdels-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. För en del kunder är dock inte okrypterad kommunikation till serverdels-servrarna en acceptabel lösning. Denna dekrypterade kommunikation kan bero på säkerhets och kompatibilitetskrav eller på att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

## <a name="overview"></a>Översikt

Slutpunkt-till-slutpunkt-SSL låter dig skicka krypterade känsliga data säkert till serverdelen samt dra nytta av de fördelar med Layer 7-belastningsutjämningsfunktioner som Application Gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Slutpunkt-till-slutpunkt SSL aktiveras genom att ställa in protokollinställningen i BackendHTTPSetting till HTTPS, som sedan appliceras till en serverdelspool. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste också vitlistas med Application Gateway, så som beskrivs i föregående steg, innan de kan användas.

## <a name="application-gateway-ssl-policy"></a>SSL-princip för Application Gateway

Application Gateway stöder användarkonfigurerbara SSL-förhandlingsprinciper som tillåter användare större kontroll över SSL-anslutningar på Application Gateway.

1. SSL 2.0 och 3.0 inaktiveras som standard för alla Gateways i programmet. Dessa principer kan inte konfigureras alls.
2. SSL-principdefinitioner ger dig möjlighet att inaktivera alla följande tre protokoll – **TLSv1\_0**, **TLSv1\_1**, **TLSv1\_2**.
3. Om ingen policy för SSL definieras aktiveras alla tre (TLSv1\_0, TLSv1\_1, TLSv1_2).

## <a name="next-steps"></a>Nästa steg

När du läst om slutpunkt-till-slutpunkts-SSL och SSL-principer kan du gå till [Aktivera slutpunkt-till-slutpunkts-SSL på Application Gateway](application-gateway-end-to-end-ssl-powershell.md) om du vill skapa en programgateway med hjälp av slutpunkt-till-slutpunkts-SSL.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

