---
title: Om du aktiverar SSL från slutpunkt till slutpunkt på Azure Application Gateway
description: Den här artikeln är en översikt över Application Gateway från slutpunkt till slutpunkt SSL-stöd.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: e7020ef5c1f7411c7226e7a2db489112ee6bf0a4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945509"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Översikt över slutpunkt-till-slutpunkt-SSL på Application Gateway

Application Gateway stöder SSL-avslutning på gatewayen, när flödar trafiken vanligtvis okrypterat fram till backend-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. För en del kunder är dock inte okrypterad kommunikation till serverdels-servrarna en acceptabel lösning. Denna dekrypterade kommunikation kan bero på säkerhets och kompatibilitetskrav eller på att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

Slutpunkt-till-slutpunkt-SSL låter dig skicka känsliga data säkert till serverdelen i krypterad samt dra nytta av fördelarna med Layer 7-belastningsutjämning funktioner som application gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Slutpunkt-till-slutpunkt SSL aktiveras genom att ställa in protokollinställningen i **BackendHTTPSetting** till HTTPS, som sedan appliceras till en serverdelspool. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste också vitlistas med application gateway såsom beskrivs i föregående steg, innan de kan användas.

> [!NOTE]
> Ställa in autentisering servercertifikat krävs inte för betrodda Azure-tjänster som Azure Web Apps.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Slutpunkt till slutpunkt SSL med v2-SKU

Autentiseringscertifikat har inaktuell och ersatts med betrodda rotcertifikat i Application Gateway v2-SKU. De fungerar på samma sätt till certifikat för autentisering med några viktiga skillnader:

- Certifikat som signerats av välkända Certifikatutfärdare myndigheter vars CN matchar värdnamnet i serverdelens HTTP-inställningarna kräver inte några ytterligare steg för slutpunkt till slutpunkt SSL ska fungera. 

   Till exempel om backend-certifikat har utfärdats av en välkänd Certifikatutfärdare och har en CN contoso.com och serverdelens http-inställning värden också anges till contoso.com, krävs sedan inga ytterligare åtgärder. Du kan ange i serverdelens http ställa in protokollet HTTPS både hälsotillstånd avsökningen och sökvägen skulle bli SSL aktiverat. Om du använder Azure Web Apps eller andra Azure webbtjänster serversidan dessa är implicit betrodda samt och inga ytterligare steg krävs för slutpunkt till slutpunkt SSL.
- Om certifikatet är självsignerat eller signerats av okänd mellanhänder, måste sedan för att aktivera SSL för slutpunkt till slutpunkt i v2-SKU ett betrott rotcertifikat definieras. Application Gateway kommunicerar bara med serverdelar vars servercertifikat rotcertifikat matchar ett av listan över betrodda rotcertifikat i serverdelens http-inställnings som hör till poolen.
- Förutom root certifikat matchar validerar också Application Gateway om värden som anges i serverdelens http-inställnings överensstämmer med det egna namnet (CN) som presenteras av serverdels-serverns SSL-certifikat. När du försöker upprätta en SSL-anslutning till serverdelen anger Application Gateway Server Name Indication (SNI)-tillägget till värden som anges i serverdelens http-inställning.
- Om **Välj värdnamnet från serverdelsadressen** väljs i stället för fältet värd i serverdelens http-inställnings SNI-huvudet är alltid inställd till serverdelspoolen FQDN- och CN på backend-servern SSL certifikatet måste matcha dess FQDN. Medlemmar i Serverdelspool med IP-adresser stöds inte i det här scenariot.
- Rotcertifikatet är en base64-kodad rotcertifikat från serverdels-servercertifikat.

## <a name="next-steps"></a>Nästa steg

När du läst om slutpunkt till slutpunkt SSL, gå till [konfigurera slutpunkt till slutpunkt SSL med Programgateway med PowerShell](application-gateway-end-to-end-ssl-powershell.md) att skapa en Programgateway med hjälp av SSL för slutpunkt till slutpunkt.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
