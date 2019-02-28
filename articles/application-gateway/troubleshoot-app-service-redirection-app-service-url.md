---
title: Felsöka Azure Application Gateway med App Service – omdirigering till App Service-URL
description: Den här artikeln innehåller information om hur du felsöker problemet omdirigering när Azure Application Gateway används med Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 7a645574a75a040c3b0218714363cf85e0384e68
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959841"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Felsöka Application Gateway med App Service – omdirigering till App Service-URL:en

 Lär dig att diagnostisera och lösa omdirigering av problem med Application Gateway där App Service-URL: en hämtar exponeras.

## <a name="overview"></a>Översikt

När du konfigurerar en offentlig mot App Service i serverdelspoolen för Application Gateway och om du har en omdirigering som konfigurerats i din programkod, kan du se att när du har åtkomst till Application Gateway, kommer du att omdirigeras av webbläsaren direkt till appen Tjänst-URL.

Det här problemet kan inträffa på grund av följande huvudsakliga skäl:

- Du har omdirigering som konfigurerats i App Service. Omdirigering kan vara lika enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure AD-autentisering vilket gör att omdirigeringen.
- Du har aktiverat ”Välj värdnamn från Backend-adress”-växel i HTTP-inställningarna för Application Gateway.
- Du har inte den anpassade domänen som registrerats med din App Service.

## <a name="sample-configuration"></a>Exempel på konfiguration

- HTTP-lyssnare: Grundläggande eller flera platser
- Backend-adresspool: App Service
- HTTP-inställningar: ”Välj värdnamnet från Serverdelsadressen” i aktiverad
- Avsökningen: ”Välj värdnamnet från HTTP-inställningar” i aktiverad

## <a name="cause"></a>Orsak

En App Service kan bara användas med den konfigurerade värdnamnen i inställningarna för anpassad domän som standard, är det ”example.azurewebsites.net” och om du vill komma åt din App Service med ett värdnamn som inte registrerad i App Service eller med Application Gateway Application Gateway FQDN, som du behöver åsidosätta värdnamnet i den ursprungliga begäran till värdnamn för App Service.

Om du vill göra detta med Application Gateway kan vi använda växeln ”Välj värdnamnet från Serverdelsadressen” i HTTP-inställningarna och för avsökningen ska fungera kan vi använda ”Välj värdnamnet från serverdelens HTTP-inställningar” i avsökningskonfigurationen.

![appservice-1](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-1.png)

På grund av detta, när App Service har en omdirigering används värdnamnet ”example.azurewebsites.net” i Location-huvudet i stället för det ursprungliga värdnamnet, såvida inte konfigurerad på annat sätt. Du kan kontrollera de exempel begärande- och svarshuvuden nedan.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
I exemplet ovan kan du Observera att rubriken har statuskoden 301 för omdirigering och location-huvudet har App Service-värdnamnet i stället för det ursprungliga värdnamnet ”www.contoso.com”.

## <a name="solution"></a>Lösning

Det här problemet kan lösas genom att inte ha en omdirigering på Application sida, men om detta inte är möjligt, vi måste ange samma värdhuvudet som Application Gateway tar emot till App Service-samt i stället för att göra en åsidosättning för värden.

När vi har gjort det, App Service gör omdirigeringen (i förekommande fall) på samma ursprungliga värdhuvudet som pekar mot Application Gateway och inte en egen.

För att uppnå detta, måste du äga en anpassad domän och följ processen som anges nedan.

- Registrera domänen i listan anpassad domän för App Service. Du måste ha en CNAME-post i din anpassade domän som pekar på App Service-FQDN till detta ändamål. Mer information finns i [mappa ett befintligt anpassat DNS-namn till Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-2.png)

- När det är klart är din App Service kan acceptera värdnamnet ”www.contoso.com”. Ändra din CNAME-post i DNS så att den pekar tillbaka till Application Gateway FQDN. Till exempel ”appgw.eastus.cloudapp.azure.com”.

- Kontrollera att domänen ”www.contoso.com” matchar FQDN för Application Gateway när du gör en DNS-fråga.

- Ange din anpassade avsökningen för att inaktivera ”Välj värdnamnet från serverdelens HTTP-inställningar”. Detta kan göras från portalen genom att avmarkera kryssrutan i inställningarna för avsökning och i PowerShell genom att inte använda PickHostNameFromBackendHttpSettings - växel i kommandot Set-AzApplicationGatewayProbeConfig. Ange din App Service FQDN ”example.azurewebsites.net” i fältet värdnamn i avsökningen som avsökning förfrågningar som skickas från Programgatewayen har detta i värdhuvudet.

  > [!NOTE]
  > När du gör nästa steg, kontrollera att din anpassad avsökning inte är kopplad till serverdelens HTTP-inställningar eftersom dina http-inställningar fortfarande har växeln ”Välj värdnamnet från Serverdelsadressen” aktiverat nu.

- Ange din Application-Gateway http-inställningar för att inaktivera ”Välj värdnamnet från Serverdelsadressen”. Detta kan göras från portalen genom att avmarkera kryssrutan och i PowerShell genom att inte använda switch - PickHostNameFromBackendAddress i kommandot Set-AzApplicationGatewayBackendHttpSettings.

- Koppla anpassad avsökning tillbaka till serverdelens HTTP-inställningar och kontrollera hälsotillstånd för serverdel om den är felfri.

- När detta är gjort Application Gateway bör nu vidarebefordra samma värdnamnet ”www.contoso.com” till App Service och omdirigering sker på samma värdnamn. Du kan kontrollera de exempel begärande- och svarshuvuden nedan.
```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: [www.contoso.com](http://www.contoso.com)

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
```
## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet kan du öppna en [supportärende](https://azure.microsoft.com/support/options/).
