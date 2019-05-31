---
title: Förstå och lösa problem med Azure AD Application Proxy CORS
description: Ger en förståelse för CORS i Azure AD Application Proxy och identifiera och lösa problem med CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399332"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Förstå och lösa problem med Azure Active Directory Application Proxy CORS

[Cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) kan ibland vara ett problem vid appar och API: er som du publicerar via Azure Active Directory Application Proxy. Den här artikeln beskrivs Azure AD Application Proxy CORS problem och lösningar.

Webbläsarskydd förhindrar normalt att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas den *princip om samma ursprung*, och förhindrar skadliga webbplatser från att läsa känsliga data från en annan plats. Men kanske ibland du vill låta andra platser anropa ditt webb-API. CORS är en W3C-standard som gör att en server som är lätta princip om samma ursprung och tillåta vissa cross-origin-begäranden medan andra avvisas.

## <a name="understand-and-identify-cors-issues"></a>Förstå och identifiera problem med CORS

Två URL: er har samma ursprung om de har identiska scheman, värdar och portar ([RFC 6454](https://tools.ietf.org/html/rfc6454)), till exempel:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Följande URL: er har olika ursprung än föregående två:

-   http:\//contoso.net - annan domän
-   http:\//contoso.com:9000/foo.html - annan port
-   https:\//contoso.com/foo.html - annat schema
-   http:\//www.contoso.com/foo.html – olika underdomäner

Princip om samma ursprung förhindrar appar från att komma åt resurser från andra ursprung om de inte använder rätt access control-huvuden. Om CORS-huvuden finns inte eller är felaktig, misslyckas cross-origin-begäranden. 

Du kan identifiera problem med CORS med hjälp av verktyg för felsökning av webbläsare:

1. Starta webbläsaren och bläddra till webbappen.
1. Tryck på **F12** att ta fram Felsökningskonsolen.
1. Försök att återskapa transaktionen och granska konsolmeddelandet. Överträdelse CORS producerar en konsol-fel om ursprunget.

I följande skärmbild, att välja den **prova** knappen orsakade ett felmeddelande för CORS den https:\//corswebclient-contoso.msappproxy.net hittades inte i åtkomst-Control-Allow-Origin-huvudet.

![CORS-problem](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS-utmaningar med Application Proxy

I följande exempel visas en typisk Azure AD Application Proxy CORS-scenario. Intern server-värdar en **CORSWebService** webb-API-styrenhet, och en **CORSWebClient** som anropar **CORSWebService**. Det finns ett AJAX-begäran från **CORSWebClient** till **CORSWebService**.

![Begäran om samma ursprung lokalt](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient appen inte fungerar när du vara värd för den lokala, men antingen misslyckas på belastning eller fel när publicerade via Azure AD-programproxy. Om du publicerat CORSWebClient och CORSWebService appar separat som olika appar via programproxy kan finns de två apparna på olika domäner. En AJAX-begäran från CORSWebClient till CORSWebService är en begäran för resursdelning mellan ursprung och misslyckas.

![Application Proxy CORS för begäran](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Lösningar för problem med Application Proxy CORS

Du kan lösa det föregående CORS-problemet i någon av flera olika sätt.

### <a name="option-1-set-up-a-custom-domain"></a>Alternativ 1: Konfigurera en anpassad domän

Använd en Azure AD-programproxy [anpassad domän](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) att publicera från samma ursprung, utan att behöva göra ändringar i appen ursprung, kod eller rubriker. 

### <a name="option-2-publish-the-parent-directory"></a>Alternativ 2: Publicera den överordnade katalogen

Publicera den överordnade mappen för båda apparna. Den här lösningen fungerar som särskilt bra om du har bara två appar på webbservern. Du kan publicera den vanliga överordnade katalogen, vilket resulterar i samma ursprung i stället för att publicera varje app separat.

I följande exempel visar portal Azure AD Application Proxy-sidan för CORSWebClient appen.  När den **interna URL: en** är inställd på *contoso.com/CORSWebClient*, appen kan inte göra lyckade begäranden till den *contoso.com/CORSWebService* katalogen, eftersom de är resursdelning för korsande ursprung. 

![Publicera app individuellt](./media/application-proxy-understand-cors-issues/image4.png)

Ange i stället den **interna URL: en** att publicera den överordnade katalogen som innehåller både den *CORSWebClient* och *CORSWebService* kataloger:

![Publicera överordnad katalog](./media/application-proxy-understand-cors-issues/image5.png)

De resulterande app-URL: er problemet effektivt CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Alternativ 3: Uppdatera HTTP-huvuden

Lägg till en anpassad HTTP-Svarsrubrik Webbtjänstmetoder så att den matchar den ursprungliga begäran. För webbplatser som körs i Internet Information Services (IIS), använder du IIS-hanteraren för att ändra rubriken:

![Lägg till anpassad svarshuvud i IIS-hanteraren](./media/application-proxy-understand-cors-issues/image6.png)

Den här ändringen kräver inte några ändringar i koden. Du kan verifiera det i Fiddler-spårningar:

**Publicera huvud-tillägg**\
HTTP/1.1 200 OK\
Cache-Control: Nej-cache\
Pragma: Nej-cache\
Content-Type: text/plain; charset = utf-8\
Förfaller:-1
Variera: Acceptera Encoding\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X drivs av: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Alternativ 4: Ändra appen

Du kan ändra din app för att stödja CORS genom att lägga till Access-Control-Allow-Origin-huvudet med lämpliga värden. Hur du lägger till rubriken beror på appens kodspråket. Ändra koden är det minst rekommenderade alternativet eftersom det kräver att arbetet som de flesta.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Alternativet 5: Öka livslängden för åtkomst-token

Vissa problem med CORS kan inte lösas, till exempel när din app omdirigerar till *login.microsoftonline.com* för autentisering, och åtkomsttoken upphör att gälla. CORS anropa sedan misslyckas. En lösning för det här scenariot är att öka livslängden för åtkomst-token att förhindra att den upphör att gälla under en användarsession. Mer information om hur du gör detta finns i [konfigurerbara tokenlivslängder i Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Se också
- [Självstudie: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planera en distribution av Azure AD Application Proxy](application-proxy-deployment-plan.md) 
- [Fjärråtkomst till lokala program via Azure Active Directory Application Proxy](application-proxy.md) 
