---
title: Om du aktiverar SSL från slutpunkt till slutpunkt på Azure Application Gateway
description: Den här artikeln är en översikt över Application Gateway från slutpunkt till slutpunkt SSL-stöd.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: amsriva
ms.openlocfilehash: 4575bed18697a5661d58dc350c24a9497f7c46ff
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578821"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Översikt över slutpunkt-till-slutpunkt-SSL på Application Gateway

Application Gateway stöder SSL-avslutning på gatewayen, när flödar trafiken vanligtvis okrypterat fram till backend-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. För en del kunder är dock inte okrypterad kommunikation till serverdels-servrarna en acceptabel lösning. Denna dekrypterade kommunikation kan bero på säkerhets och kompatibilitetskrav eller på att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

Slutpunkt-till-slutpunkt-SSL låter dig skicka krypterade känsliga data säkert till serverdelen samt dra nytta av de fördelar med Layer 7-belastningsutjämningsfunktioner som Application Gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Slutpunkt-till-slutpunkt SSL aktiveras genom att ställa in protokollinställningen i **BackendHTTPSetting** till HTTPS, som sedan appliceras till en serverdelspool. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste också vitlistas med application gateway såsom beskrivs i föregående steg, innan de kan användas.

> [!NOTE]
> Ställa in autentisering servercertifikat krävs inte för betrodda Azure-tjänster som Azure Web Apps.

## <a name="next-steps"></a>Nästa steg

När du läst om slutpunkt-till-slutpunkts-SSL kan du gå till [Konfigurera en programgateway med SSL-avslutning med hjälp av Azure-portalen](create-ssl-portal.md) om du vill skapa en programgateway med hjälp av slutpunkt-till-slutpunkts-SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
