---
title: Förstå och lösa CORS-problem med Azure AD-programproxy
description: Ger en förståelse för CORS i Azure AD Application Proxy och hur du identifierar och löser CORS-problem.
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
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025783"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Förstå och lösa CORS-problem med Azure Active Directory-programproxy

[Kors-ursprungsresursdelning (CORS)](https://www.w3.org/TR/cors/) kan ibland innebära utmaningar för de appar och API:er som du publicerar via Azure Active Directory Application Proxy. I den här artikeln beskrivs frågor och lösningar för Azure AD-programproxy CORS.

Webbläsarsäkerhet hindrar vanligtvis en webbsida från att göra AJAX-förfrågningar till en annan domän. Den här begränsningen kallas *principen för samma ursprung*och förhindrar att en skadlig plats läser känsliga data från en annan plats. Men ibland kanske du vill låta andra webbplatser anropa ditt webb-API. CORS är en W3C-standard som låter en server koppla av principen med samma ursprung och tillåta vissa begäranden med flera ursprung samtidigt som andra avvisas.

## <a name="understand-and-identify-cors-issues"></a>Förstå och identifiera CORS-problem

Två webbadresser har samma ursprung om de har identiska scheman, värdar och portar ([RFC 6454](https://tools.ietf.org/html/rfc6454)), till exempel:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Följande webbadresser har ett annat ursprung än de två föregående:

-   http:\//contoso.net - Olika domän
-   http:\//contoso.com:9000/foo.html - Olika portar
-   https:\//contoso.com/foo.html - Olika system
-   http:\//www.contoso.com/foo.html - Olika underdomäner

Principen med samma ursprung förhindrar att appar kommer åt resurser från annat ursprung om de inte använder rätt åtkomstkontrollhuvuden. Om CORS-huvudena saknas eller är felaktiga misslyckas begäranden med flera ursprung. 

Du kan identifiera CORS-problem med hjälp av felsökningsverktyg i webbläsaren:

1. Starta webbläsaren och bläddra till webbappen.
1. Tryck på **F12** för att ta upp felsökningskonsolen.
1. Försök återskapa transaktionen och granska konsolmeddelandet. En CORS-överträdelse ger ett konsolfel om ursprung.

I följande skärmbild orsakade valet av **Try It-knappen** ett\/CORS-felmeddelande som https: /corswebclient-contoso.msappproxy.net inte hittades i access-control-allow-origin-huvudet.

![CORS-fråga](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS utmaningar med Application Proxy

Följande exempel visar ett typiskt Azure AD Application Proxy CORS-scenario. Den interna servern är värd för en CORSWebService-webb-API-styrenhet och en **CORSWebClient** som **anropar CORSWebService**. **CORSWebService** Det finns en AJAX begäran från **CORSWebClient** till **CORSWebService**.

![Lokal begäran om samma ursprung](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient-appen fungerar när du är värd för den lokalt, men misslyckas antingen med att läsa in eller fel när den publiceras via Azure AD Application Proxy. Om du har publicerat CORSWebClient- och CORSWebService-apparna separat som olika appar via Application Proxy finns de två apparna på olika domäner. En AJAX-begäran från CORSWebClient till CORSWebService är en begäran om korsursprung och misslyckas.

![Begäran om ansökan proxy CORS](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Lösningar för Application Proxy CORS-problem

Du kan lösa det föregående CORS-problemet på något av flera sätt.

### <a name="option-1-set-up-a-custom-domain"></a>Alternativ 1: Konfigurera en anpassad domän

Använd en anpassad [azure](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) AD-programproxydo för att publicera från samma ursprung, utan att behöva göra några ändringar i appens ursprung, kod eller rubriker. 

### <a name="option-2-publish-the-parent-directory"></a>Alternativ 2: Publicera den överordnade katalogen

Publicera den överordnade katalogen för båda apparna. Den här lösningen fungerar särskilt bra om du bara har två appar på webbservern. I stället för att publicera varje app separat kan du publicera den gemensamma överordnade katalogen, vilket resulterar i samma ursprung.

Följande exempel visar portalen Azure AD Application Proxy sida för CORSWebClient-appen.  När den **interna webbadressen** är inställd *på contoso.com/CORSWebClient*kan appen inte göra lyckade begäranden till contoso.com/CORSWebService-katalogen, eftersom de är kors originer. *contoso.com/CORSWebService* 

![Publicera appen individuellt](./media/application-proxy-understand-cors-issues/image4.png)

Ange i stället den **interna URL:en** för att publicera den överordnade katalogen, som innehåller både *CORSWebClient-* och *CORSWebService-katalogerna:*

![Publicera överordnad katalog](./media/application-proxy-understand-cors-issues/image5.png)

De resulterande app-URL:erna löser cors-problemet effektivt:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Alternativ 3: Uppdatera HTTP-huvuden

Lägg till ett anpassat HTTP-svarshuvud på webbtjänsten som matchar ursprungsbegäran. För webbplatser som körs i Internet Information Services (IIS) använder du IIS-hanteraren för att ändra sidhuvudet:

![Lägga till anpassat svarshuvud i IIS-hanteraren](./media/application-proxy-understand-cors-issues/image6.png)

Den här ändringen kräver inga kodändringar. Du kan verifiera det i Fiddler-spåren:

**Bokför rubriktillägget**\
HTTP/1.1 200 OK\
Cache-kontroll: no-cache\
Pragma: no-cache\
Innehållstyp: text/oformaterad; charset=utf-8\
Upphör att gälla: -1\
Variera: Acceptera-kodning\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin:\:https //corswebclient-contoso.msappproxy.net**\
X-AspNet-version: 4.0.30319\
X-Powered-By: ASP.NET\
Innehåll-Längd: 17

### <a name="option-4-modify-the-app"></a>Alternativ 4: Ändra appen

Du kan ändra appen så att den stöder CORS genom att lägga till huvudet Åtkomstkontroll-Tillåt-ursprung med lämpliga värden. Sättet att lägga till sidhuvudet beror på appens kodspråk. Att ändra koden är det minst rekommenderade alternativet, eftersom det kräver mest ansträngning.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Alternativ 5: Förläng åtkomsttokens livslängd

Vissa CORS-problem kan inte lösas, till exempel när appen omdirigeras till *login.microsoftonline.com* för att autentisera och åtkomsttoken upphör att gälla. CORS-anropet misslyckas sedan. En lösning för det här scenariot är att förlänga åtkomsttokens livstid för att förhindra att den upphör att gälla under en användares session. Mer information om hur du gör detta finns [i Konfigurera tokenlivslängder i Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Se även
- [Självstudiekurs: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planera en Azure AD-programproxydistribution](application-proxy-deployment-plan.md) 
- [Fjärråtkomst till lokala program via Azure Active Directory Application Proxy](application-proxy.md) 
