---
title: Skriv om HTTP-huvuden med Azure Application Gateway | Microsoft Docs
description: Den här artikeln innehåller en översikt över hur du skriver om HTTP-rubriker i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: b6f26eca0592017306eaefd3f5fecb544dc6fb36
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932198"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Skriv om HTTP-huvuden med Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-huvuden låter en klient och Server skicka ytterligare information med en begäran eller ett svar. Genom att skriva om dessa rubriker kan du utföra viktiga uppgifter, till exempel lägga till säkerhetsrelaterade huvud fält som HSTS/X-XSS-Protection, ta bort svars huvud fält som kan avslöja känslig information och ta bort portinformation från X-vidarebefordrad – för sidhuvud.

Med Application Gateway kan du lägga till, ta bort eller uppdatera HTTP-huvuden för begäran och svar när begäran- och svarspaketen flyttas mellan klientens och serverdelens pooler. Du kan också lägga till villkor så att de angivna rubrikerna bara skrivs om när vissa villkor uppfylls.

Application Gateway stöder också flera [servervariabler](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) som hjälper dig att lagra ytterligare information om begär Anden och svar. Detta gör det enklare för dig att skapa kraftfulla omskrivnings regler.

> [!NOTE]
>
> Stödet för omskrivning av HTTP-huvud är bara tillgängligt för [SKU: n för Standard_V2 och WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Rubriker skrivs om](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Rubriker som stöds

Du kan skriva om alla huvuden i begär Anden och svar, förutom värd-, anslutnings-och uppgraderings rubrikerna. Du kan också använda Application Gateway för att skapa anpassade huvuden och lägga till dem i de begär Anden och svar som dirigeras genom den.

## <a name="rewrite-conditions"></a>Skriv om villkor

Du kan använda omskrivnings villkor för att utvärdera innehållet i HTTP (S)-begär Anden och svar och utföra en omskrivning av sidhuvudet endast om ett eller flera villkor är uppfyllda. Programgatewayen använder dessa typer av variabler för att utvärdera innehållet i HTTP (S)-begär Anden och svar:

- HTTP-huvuden i begäran.
- HTTP-huvuden i svaret.
- Application Gateway servervariabler.

Du kan använda ett villkor för att utvärdera om en angiven variabel finns, om en angiven variabel matchar ett specifikt värde, eller om en angiven variabel matchar ett specifikt mönster. Du använder [biblioteket med perl-kompatibla reguljära uttryck (pcre)](https://www.pcre.org/) för att konfigurera mönster matchning för reguljära uttryck i villkoren. Mer information om syntax för reguljära uttryck finns i [huvud sidan för vanliga](https://perldoc.perl.org/perlre.html)uttryck i perl.

## <a name="rewrite-actions"></a>Skriv om åtgärder

Du använder återskrivnings åtgärder för att ange de begärandehuvuden för begäran och svar som du vill skriva om och det nya värdet för rubrikerna. Du kan antingen skapa ett nytt sidhuvud, ändra värdet för en befintlig rubrik eller ta bort ett befintligt sidhuvud. Värdet för ett nytt sidhuvud eller en befintlig rubrik kan anges till följande typer av värden:

- Information.
- Begär ande huvud. Om du vill ange ett rubrik för begäran måste du använda syntaxen {http_req_*huvud*}.
- Svarshuvud. Om du vill ange ett svars huvud måste du använda syntaxen {http_resp_*huvud*}.
- Server variabel. Om du vill ange en server variabel måste du använda syntaxen {var_*serverVariable*}.
- En kombination av text, ett begär ande huvud, ett svars huvud och en server variabel.

## <a name="server-variables"></a>Servervariabler

Application Gateway använder servervariabler för att lagra användbar information om servern, anslutningen till klienten och den aktuella begäran på anslutningen. Exempel på information som lagras är klientens IP-adress och webbläsarens typ. Servervariabler ändras dynamiskt, till exempel när en ny sida läses in eller när ett formulär publiceras. Du kan använda dessa variabler för att utvärdera Skriv villkor och skriva om rubriker.

Application Gateway stöder följande servervariabler:

| Variabelnamn | Beskrivning                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Det X-vidarebefordrade – för fältet för klient begär ande huvud `client_ip` med variabeln (se förklaringen senare i den här tabellen) som läggs till i formatet IP1, IP2, IP3 och så vidare. Om fältet X-forwarded inte finns i klient begär ande huvudet `add_x_forwarded_for_proxy` är variabeln lika med `$client_ip` variabeln. Den här variabeln är särskilt användbar när du vill skriva om den X-vidarebefordrade-för-rubrik som angetts av Application Gateway så att sidhuvudet endast innehåller IP-adressen utan portinformation. |
| ciphers_supported          | En lista över de chiffer som stöds av klienten.          |
| ciphers_used               | Den sträng med chiffer som används för en etablerad SSL-anslutning. |
| client_ip                  | IP-adressen för den klient som Application Gateway tog emot begäran från. Om det finns en omvänd proxy före programgatewayen och den ursprungliga klienten kommer *client_ip* att returnera IP-adressen för den omvända proxyn. |
| client_port                | Klient porten.                                                  |
| client_tcp_rtt             | Information om klientens TCP-anslutning. Tillgängligt på system som har stöd för alternativet TCP_INFO socket. |
| client_user                | När HTTP-autentisering används anges användar namnet för autentisering. |
| host                       | I den här prioritetsordningen: värd namnet från begär ande raden, värd namnet från fältet värd begär ande huvud eller Server namnet som matchar en begäran. |
| cookie_*namn*              | Cookie- *namn* .                                            |
| http_method                | Den metod som används för att göra URL-begäran. Till exempel GET eller POST. |
| http_status                | Sessionens status. Till exempel 200, 400 eller 403.                       |
| http_version               | Protokollet för begäran. Vanligt vis HTTP/1.0, HTTP/1.1 eller HTTP/2.0. |
| query_string               | Listan över variabel/värde-par som följer "?" i den begärda URL: en. |
| received_bytes             | Längden på begäran (inklusive raden för begäran, sidhuvud och brödtext). |
| request_query              | Argumenten på raden för begäran.                                |
| request_scheme             | Begär ande schema: http eller https.                            |
| request_uri                | Fullständig URI för ursprunglig begäran (med argument).                   |
| sent_bytes                 | Antalet byte som har skickats till en klient.                             |
| server_port                | Porten för den server som godkände en begäran.                 |
| ssl_connection_protocol    | Protokollet för en etablerad SSL-anslutning.        |
| ssl_enabled                | "On" om anslutningen fungerar i SSL-läge. Annars är en tom sträng. |

## <a name="rewrite-configuration"></a>Skriv om konfigurationen

Om du vill konfigurera omskrivning av HTTP-huvud måste du slutföra de här stegen.

1. Skapa de objekt som krävs för omskrivning av HTTP-huvud:

   - **Skriv om åtgärd**: Används för att ange begär ande huvud fält som du vill skriva om och det nya värdet för rubrikerna. Du kan associera ett eller flera omskrivnings villkor med en Skriv åtgärd.

   - **Skriv villkor**: En valfri konfiguration. Omskrivnings villkor utvärderar innehållet i HTTP (S)-begär Anden och svar. Återskrivning görs om HTTP (S)-begäran eller-svaret matchar omskrivnings villkoret.

     Om du associerar fler än ett villkor med en åtgärd sker åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden ett logiskt och en åtgärd.

   - **Skriv över regel**: Innehåller kombinationer av flera omskrivna åtgärder/omskrivna villkor.

   - **Regel ordning**: Hjälper till att fastställa i vilken ordning reglerna för att skriva om ska köras. Den här konfigurationen är användbar när du har flera omskrivnings regler i en omskrivnings uppsättning. En omskrivnings regel som har ett lägre regel ordnings värde körs först. Om du tilldelar samma regel ordning till två omskrivnings regler är körnings ordningen icke-deterministisk.

   - **Skriv över uppsättning**: Innehåller flera omskrivnings regler som ska associeras med en regel för anslutningsbegäran.

2. Koppla*rewriteRuleSet*(Rewrite set) till en regel för routning. Den omskrivna konfigurationen är kopplad till käll lyssnaren via regeln för routning. När du använder en regel för grundläggande routning associeras konfigurationen för omskrivning av huvuden med en käll lyssnare och är en omskrivning av globala huvuden. När du använder en regel för Sök vägs-baserad routning definieras konfigurationen för att skriva över rubriker i sökvägen till URL-sökvägen. I så fall gäller det bara för det angivna Sök vägs området på en plats.
   > [!NOTE]
   > URL-omskrivning ändra rubrikerna. URL: en för sökvägen ändras inte.

Du kan skapa flera Skriv-och skriv åtgärder för HTTP-huvudet och tillämpa varje skrivnings uppsättning på flera lyssnare. Men du kan endast använda en omskrivnings uppsättning för en speciell lyssnare.

## <a name="common-scenarios"></a>Vanliga scenarier

Här följer några vanliga scenarier för att använda omarbetning av huvuden.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Ta bort portinformation från det X-vidarebefordrade – för sidhuvudet

Application Gateway infogar en X-vidarebefordrad-för-rubrik i alla begär Anden innan den vidarebefordrar begär anden till Server delen. Den här rubriken är en kommaavgränsad lista med IP-portar. Det kan finnas scenarier där backend-servrarna bara behöver rubrikerna för att innehålla IP-adresser. Du kan använda omskrivning av huvuden för att ta bort portinformation från den X-vidarebefordrade-for-rubriken. Ett sätt att göra detta är att ange rubriken till add_x_forwarded_for_proxy-Server variabeln:

![Ta bort port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Ändra en URL för omdirigering

När ett backend-program skickar ett svar på omdirigering kanske du vill omdirigera klienten till en annan URL än den som anges av Server dels programmet. Du kanske till exempel vill göra detta när en app service finns bakom en Programgateway och kräver att klienten utför en omdirigering till dess relativa sökväg. (Till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2.)

Eftersom App Service är en tjänst för flera innehavare använder den värd rubriken i begäran för att dirigera begäran till rätt slut punkt. App Services har ett standard domän namn på *. azurewebsites.net (säg contoso.azurewebsites.net) som skiljer sig från programgatewayens domän namn (t. ex. contoso.com). Eftersom den ursprungliga begäran från klienten har Application gateways domän namn (contoso.com) som värdnamn, ändrar programgatewayen värd namnet till contoso.azurewebsites.net. Den här ändringen görs så att App Service kan dirigera begäran till rätt slut punkt.

När App Service skickar ett svar för omdirigering använder den samma värdnamn i plats huvudet för sitt svar som det i den begäran som den tar emot från programgatewayen. Klienten kommer därför att göra begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå igenom Application Gateway (contoso.com/path2). Att kringgå Application Gateway är inte önskvärt.

Du kan lösa det här problemet genom att ange värd namnet i plats rubriken till Application gatewayens domän namn.

Här följer stegen för att ersätta värd namnet:

1. Skapa en Rewrite-regel med ett villkor som utvärderar om plats rubriken i svaret innehåller azurewebsites.net. Ange mönstret `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Utför en åtgärd för att skriva om plats rubriken så att den har Application Gateway-värdnamnet. Gör detta genom att `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` ange som rubrik värde.

![Ändra plats rubrik](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementera säkerhets-HTTP-huvuden för att förhindra sårbarheter

Du kan åtgärda flera säkerhets problem genom att implementera nödvändiga huvuden i program svaret. Dessa säkerhets rubriker inkluderar X-XSS-skydd, strikt-Transport-Security och Content-Security-Policy. Du kan använda Application Gateway för att ange de här rubrikerna för alla svar.

![Säkerhets huvud](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Ta bort oönskade rubriker

Du kanske vill ta bort huvuden som avslöjar känslig information från ett HTTP-svar. Till exempel kanske du vill ta bort information som server namn, operativ system eller biblioteks information för Server delen. Du kan använda Application Gateway för att ta bort dessa huvuden:

![Tar bort rubrik](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Sök efter en rubriks förekomst

Du kan utvärdera en HTTP-begäran eller ett svars huvud för förekomst av en huvud-eller Server variabel. Den här utvärderingen är användbar när du vill utföra en skrivskyddad rubrik endast när det finns en viss rubrik.

![Kontrollerar närvaron av ett sidhuvud](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Begränsningar

- Om ett svar har fler än en rubrik med samma namn, kommer de andra rubrikerna att tas bort om du skriver om värdet för en av dessa huvuden. Detta kan vanligt vis inträffa med set-cookie-sidhuvudet eftersom du kan ha fler än en uppsättning-cookie-huvud i ett svar. Ett sådant scenario är när du använder en app service med en Application Gateway och har konfigurerat cookie-baserad mappning mellan sessioner på Application Gateway. I det här fallet kommer svaret att innehålla två uppsättningar-cookie-huvuden: en som används av App Service, till `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` exempel: och en annan för Application Gateway-tillhörighet, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`till exempel. Om du skriver om en av Set-cookie-huvudena i det här scenariot kan det leda till att du tar bort den andra uppsättningen av cookie-huvud från svaret.

- Det finns för närvarande inte stöd för att skriva om anslutningen, uppgradera och värdhuvuden.

- Rubrik namn får innehålla alfanumeriska tecken och vissa symboler som definieras i [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Vi stöder för närvarande inte under streck (\_) med specialtecken i rubrik namn.

## <a name="next-steps"></a>Nästa steg

Information om hur du skriver om HTTP-huvuden finns i:

- [Skriv om HTTP-huvuden med Azure Portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Skriv om HTTP-huvuden med Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
