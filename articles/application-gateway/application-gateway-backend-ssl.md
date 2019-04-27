---
title: Aktivera slutpunkt-till-slutpunkt-SSL på Azure Application Gateway | Microsoft Docs
description: Den här sidan ger en översikt över slutpunkt-till-slutpunkt SSL-stöd för Application Gateway.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
origin.date: 07/19/2017
ms.date: 04/15/2019
ms.author: v-junlch
ms.openlocfilehash: 856f23de8a8772255f570a923ecf1708dc819bb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831880"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Översikt över slutpunkt-till-slutpunkt-SSL på Application Gateway

Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdels-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. För en del kunder är dock inte okrypterad kommunikation till serverdels-servrarna en acceptabel lösning. Denna dekrypterade kommunikation kan bero på säkerhets och kompatibilitetskrav eller på att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

## <a name="overview"></a>Översikt

Slutpunkt-till-slutpunkt-SSL låter dig skicka krypterade känsliga data säkert till serverdelen samt dra nytta av de fördelar med Layer 7-belastningsutjämningsfunktioner som Application Gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Slutpunkt-till-slutpunkt SSL aktiveras genom att ställa in protokollinställningen i BackendHTTPSetting till HTTPS, som sedan appliceras till en serverdelspool. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste också vitlistas med Application Gateway, så som beskrivs i föregående steg, innan de kan användas.

## <a name="next-steps"></a>Nästa steg

När du läst om slutpunkt-till-slutpunkts-SSL kan du gå till [Aktivera slutpunkt-till-slutpunkts-SSL på Application Gateway](application-gateway-end-to-end-ssl-powershell.md) om du vill skapa en Application Gateway med hjälp av slutpunkt-till-slutpunkts-SSL.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

<!-- Update_Description: update metedata properties -->