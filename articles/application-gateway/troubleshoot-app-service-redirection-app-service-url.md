---
title: Felsöka Azure Application Gateway med App Service – omdirigering till App Services URL
description: Den här artikeln innehåller information om hur du felsöker problemet vid omdirigering när Azure Application Gateway används med Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347878"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Felsöka App Service problem i Application Gateway

Lär dig hur du diagnostiserar och löser problem som uppstår när app server används som server dels mål med Application Gateway

## <a name="overview"></a>Översikt

I den här artikeln får du lära dig hur du felsöker följande problem:

> [!div class="checklist"]
> * App Services webb adress som visas i webbläsaren när det finns en omdirigering
> * App Service ARRAffinity cookie-domän har angetts till App Service hostname (example.azurewebsites.net) i stället för den ursprungliga värden

När ett backend-program skickar ett svar på omdirigering kanske du vill omdirigera klienten till en annan URL än den som anges av Server dels programmet. Du kanske till exempel vill göra detta när en app service finns bakom en Programgateway och kräver att klienten utför en omdirigering till dess relativa sökväg. (Till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2.) När App Service skickar ett svar för omdirigering använder den samma värdnamn i plats huvudet för sitt svar som det i den begäran som den tar emot från programgatewayen. Klienten kommer därför att göra begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå igenom Application Gateway (contoso.com/path2). Att kringgå Application Gateway är inte önskvärt.

Det här problemet kan bero på följande huvudsakliga orsaker:

- Du har konfigurerat omdirigeringen på App Service. Omdirigering kan vara lika enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure AD-autentisering som gör omdirigeringen.

När du använder App Services bakom Application Gateway kommer även domän namnet som är associerat med Application Gateway (example.com) att skilja sig från domän namnet för App Service (t. ex. example.azurewebsites.net) på grund av att du kommer att märka att domän värdet för ARRAffinity-cookien som angetts av App Service kommer att ha domän namnet "example.azurewebsites.net" som inte är önskvärt. Det ursprungliga värd namnet (example.com) ska vara domän namn svärdet i cookien.

## <a name="sample-configuration"></a>Exempel på konfiguration

- HTTP-lyssnare: Basic eller flera platser
- Backend-adresspool: App Service
- HTTP-inställningar: "Välj värd namn från backend-adress" aktiverat
- Provtagning Inställningen Välj värdnamn för HTTP-inställningar är aktive rad

## <a name="cause"></a>Orsak

Eftersom App Service är en tjänst för flera innehavare använder den värd rubriken i begäran för att dirigera begäran till rätt slut punkt. Standard domän namnet för app Services, *. azurewebsites.net (t. ex. contoso.azurewebsites.net) skiljer sig dock från Application gateways domän namn (t. ex. contoso.com). Eftersom den ursprungliga begäran från klienten har programgatewayens domän namn (contoso.com) som värdnamn, måste du konfigurera programgatewayen för att ändra värd namnet i den ursprungliga begäran till app Services-värdnamnet när den dirigerar begäran till Server delen för App Service.  Du uppnår detta genom att använda växeln "Välj värd namn från backend-adress" i Application Gatewayens HTTP-inställnings konfiguration och växeln "Välj värd namn från Server delens HTTP-inställningar" i hälso avsöknings konfigurationen.



![AppService-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

På grund av detta, när App Service gör en omdirigering, använder den åsidosättningen värdnamn "contoso.azurewebsites.net" i plats rubriken, i stället för det ursprungliga värd namnet "contoso.com", om inget annat anges. Du kan kontrol lera exempel begär Anden och svarshuvuden nedan.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
I ovanstående exempel kan du märka att svars huvudet har status kod 301 för omdirigering och att plats huvudet har App Serviceens värdnamn i stället för det ursprungliga värd namnet "www.contoso.com".

## <a name="solution-rewrite-the-location-header"></a>Lösa Skriv om plats rubriken

Du måste ange värd namnet i plats rubriken till Application gatewayens domän namn. Det gör du genom att skapa en Rewrite- [regel](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) med ett villkor som utvärderar om plats rubriken i svaret innehåller azurewebsites.net och utför en åtgärd för att skriva om plats rubriken till att ha Application Gateway-värdnamnet.  Se anvisningar om [hur du skriver om plats rubriken](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Stödet för omskrivning av HTTP-huvud är bara tillgängligt för [Standard_V2-och WAF_v2-SKU: n](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) för Application Gateway. Om du använder v1 SKU rekommenderar vi starkt att du [migrerar från v1 till v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) för att kunna använda omskrivning och andra [avancerade funktioner](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) som är tillgängliga med v2 SKU.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Alternativ lösning: Använd app Services anpassade domän i stället för standard domän namn

Om du använder v1 SKU kommer du inte att kunna skriva om plats huvudet eftersom den här funktionen endast är tillgänglig för v2 SKU. För att lösa problemet med omdirigering måste du därför skicka samma värdnamn som Application Gateway tar emot till App Service, i stället för att utföra en åsidosättning av värden.

När du gör det kommer App Service att utföra omdirigeringen (om det finns några) på samma ursprungliga värd adress som pekar på Application Gateway och inte dess egna.

För att uppnå detta måste du äga en anpassad domän och följa den process som beskrivs nedan.

- Registrera domänen på den anpassade domän listan för App Service. För detta måste du ha en CNAME i din anpassade domän som pekar på App Service FQDN. Mer information finns i [mappa ett befintligt anpassat DNS-namn till Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![AppService-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- När du är klar är ditt App Service redo att acceptera värd namnet "www.contoso.com". Ändra nu posten CNAME i DNS så att den pekar tillbaka till Application Gateway FQDN. Till exempel "appgw.eastus.cloudapp.azure.com".

- Kontrol lera att din domän "www.contoso.com" matchar Application Gatewayens FQDN när du gör en DNS-fråga.

- Ange anpassad avsökning för att inaktivera alternativet Välj värd namn från Server delens HTTP-inställningar. Detta kan göras från portalen genom att avmarkera kryss rutan i avsöknings inställningarna och i PowerShell genom att inte använda växeln-PickHostNameFromBackendHttpSettings i kommandot Set-AzApplicationGatewayProbeConfig. I fältet hostname för avsökningen anger du App Service FQDN "example.azurewebsites.net" som avsöknings begär Anden som skickas från Application Gateway kommer att ha detta i värd huvudet.

  > [!NOTE]
  > När du fortsätter med nästa steg kontrollerar du att din anpassade avsökning inte är kopplad till HTTP-inställningarna för din server del eftersom dina HTTP-inställningar fortfarande har växeln "Välj värd namn från Server del adress" som är aktive rad i det här läget.

- Ange Application Gatewayns HTTP-inställningar om du vill inaktivera "Välj värd namn från backend-adress". Detta kan göras från portalen genom att avmarkera kryss rutan och i PowerShell genom att inte använda växeln-PickHostNameFromBackendAddress i kommandot Set-AzApplicationGatewayBackendHttpSettings.

- Koppla tillbaka den anpassade avsökningen till HTTP-inställningarna för Server delen och kontrol lera Server dels hälsan om den är felfri.

- När detta är slutfört bör Application Gateway nu vidarebefordra samma värdnamn "www.contoso.com" till App Service och omdirigeringen sker på samma värdnamn. Du kan kontrol lera exempel begär Anden och svarshuvuden nedan.

Följ PowerShell-skriptet nedan om du vill implementera de steg som beskrivs ovan med PowerShell för en befintlig installation. Observera att vi inte har använt växlarna-PickHostname i konfigurationen för avsöknings-och HTTP-inställningar.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).
