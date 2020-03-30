---
title: Felsöka omdirigering till URL för apptjänst
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du felsöker omdirigeringsproblemet när Azure Application Gateway används med Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293529"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Felsöka problem med apptjänsten i Application Gateway

Lär dig hur du diagnostiserar och löser problem som kan uppstå när Azure App Service används som ett backend-mål med Azure Application Gateway.

## <a name="overview"></a>Översikt

I den här artikeln får du lära dig felsöka följande problem:

> [!div class="checklist"]
> * Apptjänstens URL visas i webbläsaren när det finns en omdirigering.
> * Apptjänsten ARRAffinity cookie-domänen är inställd på appens värdnamn för tjänsten, example.azurewebsites.net, i stället för den ursprungliga värden.

När ett backend-program skickar ett omdirigeringssvar kanske du vill omdirigera klienten till en annan URL än den som anges av backend-programmet. Du kanske vill göra detta när en apptjänst finns bakom en programgateway och kräver att klienten gör en omdirigering till dess relativa sökväg. Ett exempel är en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2. 

När apptjänsten skickar ett omdirigeringssvar används samma värdnamn i platshuvudet för sitt svar som det i begäran som den tar emot från programgatewayen. Klienten gör till exempel begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå igenom programgatewayen contoso.com/path2. Du vill inte kringgå programgatewayen.

Det här problemet kan uppstå av följande huvudskäl:

- Du har konfigurerat omdirigering på apptjänsten. Omdirigering kan vara så enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure Active Directory-autentisering, vilket orsakar omdirigering.

När du använder apptjänster bakom en programgateway skiljer sig domännamnet som är associerat med programgatewayen (example.com) från apptjänstens domännamn (t.ex. example.azurewebsites.net). Domänvärdet för den ARRAffinity-cookie som anges av apptjänsten har example.azurewebsites.net domännamn, vilket inte är önskvärt. Det ursprungliga värdnamnet, example.com, ska vara domännamnsvärdet i cookien.

## <a name="sample-configuration"></a>Exempelkonfiguration

- HTTP-lyssnare: Grundläggande eller flera webbplatser
- Backend-adresspool: Appservice
- HTTP-inställningar: **Välj värdnamn från backend-adress** aktiverat
- Avsökning: **Välj värdnamn från HTTP-inställningar** aktiverat

## <a name="cause"></a>Orsak

App Service är en tjänst med flera tjänster, så den använder värdhuvudet i begäran för att dirigera begäran till rätt slutpunkt. Standarddomännamnet för App Services, *.azurewebsites.net (t.ex. contoso.azurewebsites.net), skiljer sig från programgatewayens domännamn (t.ex. contoso.com). 

Den ursprungliga begäran från klienten har programgatewayens domännamn, contoso.com, som värdnamn. Du måste konfigurera programgatewayen för att ändra värdnamnet i den ursprungliga begäran till apptjänstens värdnamn när den dirigerar begäran till apptjänstens serverdel. Använd växeln **Välj värdnamn från serverdadress** i programgatewayens HTTP-inställningskonfiguration. Använd växeln **Välj värdnamn från SERVERD HTTP-inställningar** i hälsoavsökningskonfigurationen.



![Värdnamn för programgatewayändringar](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

När apptjänsten gör en omdirigering används det åsidosatta värdnamnet contoso.azurewebsites.net i platshuvudet i stället för det ursprungliga värdnamnet contoso.com, om inte annat konfigureras. Kontrollera följande exempel på begäran och svarsrubriker.
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
I föregående exempel märker du att svarshuvudet har en statuskod på 301 för omdirigering. Platshuvudet har apptjänstens värdnamn i stället `www.contoso.com`för det ursprungliga värdnamnet .

## <a name="solution-rewrite-the-location-header"></a>Lösning: Skriv om platshuvudet

Ange värdnamnet i platshuvudet till programgatewayens domännamn. Det gör du genom att skapa en [skrivregel](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) med ett villkor som utvärderar om platshuvudet i svaret innehåller azurewebsites.net. Den måste också utföra en åtgärd för att skriva om platshuvudet så att programgatewayens värdnamn har. Mer information finns i instruktioner om [hur du skriver om platshuvudet](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Stöd för HTTP-huvud skriva om är endast tillgängligt för [Standard_v2 och WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) för programgateway. Om du använder v1 SKU rekommenderar vi att du [migrerar från v1 till v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Du vill använda omskrivning och andra [avancerade funktioner](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) som är tillgängliga med v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativ lösning: Använda ett eget domännamn

Om du använder v1 SKU kan du inte skriva om platshuvudet. Den här funktionen är endast tillgänglig för v2 SKU. Lös omdirigeringsproblemet genom att skicka samma värdnamn som programgatewayen tar emot till apptjänsten också, i stället för att göra en värdsidosättning.

Apptjänsten gör nu omdirigering (om någon) på samma ursprungliga värdhuvud som pekar på programgatewayen och inte dess egen.

Du måste äga en anpassad domän och följa den här processen:

- Registrera domänen i apptjänstens anpassade domänlista. Du måste ha ett CNAME i din anpassade domän som pekar på apptjänstens FQDN. Mer information finns i [Mappa ett befintligt anpassat DNS-namn till Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Anpassad domänlista för apptjänst](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Din apptjänst är klar att `www.contoso.com`acceptera värdnamnet . Ändra CNAME-posten i DNS för att peka tillbaka den till programgatewayens FQDN, `appgw.eastus.cloudapp.azure.com`till exempel .

- Se till att `www.contoso.com` domänen matchas till programgatewayens FQDN när du gör en DNS-fråga.

- Ange att den anpassade avsökningen ska inaktivera **Plocka värdnamn från HTTP-inställningar för servering**. Avmarkera kryssrutan i avsökningsinställningarna i Azure-portalen. Använd inte växeln **-PickHostNameFromBackendHttpSettings** i kommandot **Set-AzApplicationGatewayProbeConfig i PowerShell.** Ange apptjänstens FQDN,example.azurewebsites.net i värdnamnsfältet för avsökningen. Avsökningsbegäranden som skickas från programgatewayen bär fqdn-värden i värdhuvudet.

  > [!NOTE]
  > Nästa steg gäller att se till att den anpassade avsökningen inte är kopplad till http-inställningarna för serverdelen. Http-inställningarna har fortfarande växeln **Välj värdnamn från backend-adress** aktiverad just nu.

- Ange http-inställningar för programgatewayen för att inaktivera **Välj värdnamn från backend-adress**. Avmarkera kryssrutan i Azure-portalen. Använd inte växeln **-PickHostNameFromBackendAddress** i kommandot **Set-AzApplicationGatewaybackendHttpSettings** i PowerShell.

- Associera den anpassade avsökningen tillbaka till http-inställningarna för backend och kontrollera att serverdelen är felfri.

- Programgatewayen ska nu vidarebefordra `www.contoso.com`samma värdnamn till apptjänsten. Omdirigeringen sker på samma värdnamn. Kontrollera följande exempel på begäran och svarsrubriker.

Om du vill implementera föregående steg med PowerShell för en befintlig installation använder du exemplet PowerShell-skript som följer. Observera hur vi inte har använt **växeln -PickHostname** i avsöknings- och HTTP-inställningskonfigurationen.

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

Om föregående steg inte löste problemet öppnar du en [supportbiljett](https://azure.microsoft.com/support/options/).
