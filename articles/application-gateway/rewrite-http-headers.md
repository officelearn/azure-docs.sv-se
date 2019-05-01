---
title: Skriv om HTTP-huvuden med Azure Application Gateway | Microsoft Docs
description: Den här artikeln innehåller en översikt över skriva om HTTP-huvuden i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 89df3a981ba3710e848f834c303772e94e10b139
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947183"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Skriv om HTTP-huvuden med Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-huvuden kan en klient och server att skicka ytterligare information med en begäran eller ett svar. Du kan utföra viktiga uppgifter, till exempel att lägga till säkerhetsrelaterat namn på huvudfält som HSTS / X-XSS-skydd, ta bort svar huvudfält som kan röja känslig information och ta bort portinformationen från genom att skriva om dessa huvuden X-vidarebefordrade-för-huvuden.

Application Gateway kan du lägga till, ta bort eller uppdatera HTTP-begäran och svarshuvuden när begäran och svarspaket flytta mellan klienten och backend-adresspooler. Och du kan lägga till villkor så att de angivna sidhuvudena är har skrivits bara när vissa villkor uppfylls.

Application Gateway stöder också flera [servervariabler](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) som hjälper dig att du lagrar ytterligare information om begäranden och svar. Detta gör det enklare för dig att skapa kraftfulla omskrivningsregler.

> [!NOTE]
>
> HTTP-huvud omskrivning-stöd är endast tillgänglig för den [WAF_v2 SKU: er och Standard_V2](application-gateway-autoscaling-zone-redundant.md).

![Skriva om rubriker](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Stöds rubriker

Du kan skriva om alla huvuden i begäranden och svar, förutom rubrikerna som värd, anslutning och uppgradering. Du kan också använda application gateway för att skapa anpassade sidhuvuden och lägga till dem i begäranden och svar som dirigeras genom den.

## <a name="rewrite-conditions"></a>Skriv om villkor

Du kan använda omskrivning villkor att utvärdera innehållet i HTTP (S)-begäranden och svar och utföra en rubrik-omskrivning endast när en eller flera villkor är uppfyllda. Application gateway använder dessa typer av variabler för att utvärdera innehållet i HTTP (S)-begäranden och svar:

- HTTP-huvuden i begäran.
- HTTP-huvuden i svaret.
- Application Gateway-servervariabler.

Du kan använda ett villkor för att utvärdera om en specifik variabel finns, om en specifik variabel matchar ett specifikt värde eller om en specifik variabel matchar ett specifikt mönster. Du använder den [Perl kompatibla reguljära uttryck (PCRE)-biblioteket](https://www.pcre.org/) att ställa in mönster för reguljärt uttryck som matchar under förhållanden. Mer information om syntax för reguljära uttryck, finns det [Perl reguljära uttryck huvudsidan](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Skriv om åtgärder

Du kan använda Skriv om åtgärder för att ange sidhuvuden för begäran och svar som du vill skriva om och det nya värdet för sidhuvudena. Du kan antingen skapa ett nytt huvud, ändrar du värdet för en befintlig rubrik eller ta bort en befintlig rubrik. Värdet för ett nytt huvud eller en befintlig rubrik kan anges till dessa typer av värden:

- Text.
- Huvudet i begäran. Om du vill ange en rubrik för begäran, måste du använda syntaxen {http_req_*headerName*}.
- Svarshuvud. Om du vill ange ett svarshuvud, måste du använda syntaxen {http_resp_*headerName*}.
- Servervariabel. Om du vill ange en servervariabel, måste du använda syntaxen {var_*serverVariable*}.
- En kombination av text, en rubrik för begäran, ett svarshuvud och en servervariabel.

## <a name="server-variables"></a>Servervariabler

Application Gateway använder servervariabler för att lagra användbar information om servern, anslutningen med klienten och den aktuella begäran på anslutningen. Exempel på information som lagras är klientens IP-adress och typ av web-webbläsare. Servervariabler ändras dynamiskt, till exempel när en ny sida läses in eller när ett formulär publiceras. Du kan använda dessa variabler för att utvärdera omskrivning villkor och skriv om rubriker.

Application gateway stöder dessa servervariabler:

| Variabelnamn | Beskrivning                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | X-vidarebefordrade-för klientens begäran header-fält med den `client_ip` variabeln (se förklaring senare i den här tabellen) läggs till det i formatet IP1, IP2, IP3 och så vidare. Om X-vidarebefordrade-för-fält som inte finns i klienten rubriken, den `add_x_forwarded_for_proxy` variabeln är lika med den `$client_ip` variabeln. Den här variabeln är särskilt användbart när du vill skriva om rubriken X-vidarebefordrade-för ställa in av Application Gateway så att huvudet innehåller IP-adress utan portinformation. |
| ciphers_supported          | En lista över de chiffer som stöds av klienten.          |
| ciphers_used               | Sträng med chiffer som används för en etablerad SSL-anslutning. |
| client_ip                  | IP-adressen för klienten som application gateway som tog emot begäran. Om det finns en omvänd proxy innan application gateway och den ursprungliga klienten *client_ip* returnerar IP-adressen för den omvända proxyn. |
| client_port                | Porten som klienten.                                                  |
| client_tcp_rtt             | Information om klienten TCP-anslutning. Tillgängligt på system som har stöd för alternativet TCP_INFO socket. |
| client_user                | När HTTP-autentisering används användarnamnet som angetts för autentisering. |
| värd                       | I den här rangordning: värdnamnet från raden för kravet, värdnamnet från fältet värd begäran rubrik eller ett servernamn som matchar en begäran. |
| cookie_*name*              | Den *namn* cookie.                                            |
| http_method                | den metod som används för att utföra URL-begäran. Till exempel hämta eller skicka. |
| http_status                | Sessionsstatus. Till exempel 200, 400 eller 403.                       |
| http_version               | Begäran-protokoll. Vanligtvis HTTP/1.0, HTTP/1.1 eller HTTP/2.0. |
| query_string               | I listan över variabeln/värde-par som följer den ””? i den begärda URL: en. |
| received_bytes             | Längden på begäran (inklusive den raden för kravet, rubrik och begärandetexten). |
| request_query              | Argumenten i raden för kravet.                                |
| request_scheme             | Schemat för begäran: http eller https.                            |
| request_uri                | Fullständiga ursprungliga begärande-URI (med argument).                   |
| sent_bytes                 | Antal byte som skickats till en klient.                             |
| server_port                | Porten för den server som godkänt en begäran.                 |
| ssl_connection_protocol    | Protokollet för en etablerad SSL-anslutning.        |
| ssl_enabled                | ”På” om anslutningen fungerar i SSL-läge. I annat fall en tom sträng. |

## <a name="rewrite-configuration"></a>Skriv om konfiguration

För att konfigurera HTTP-huvud omskrivning, måste du slutföra de här stegen.

1. Skapa de objekt som krävs för Skriv om HTTP-huvud:

   - **Skriv om åtgärden**: Används för att ange begäran och begäran huvudfält som du vill skriva om och det nya värdet för sidhuvudena. Du kan koppla ett eller flera omarbetning villkor med en omarbetning åtgärd.

   - **Skriv om villkoret**: En valfri konfiguration. Skriv om villkor utvärdera innehållet i HTTP (S)-begäranden och svar. Skriv om-åtgärd som ska utföras om den HTTP (S)-begäran eller ett svar matchar villkoret omarbetning.

     Om du associerar mer än ett villkor med åtgärden utförs åtgärden endast när alla villkor är uppfyllda. Åtgärden är med andra ord en logiskt AND-åtgärd.

   - **Skriva om regeln**: Innehåller flera omskrivning åtgärd / omarbetning kombinationer av villkor.

   - **Regeln sekvens**: Hjälper dig att fastställa i vilken ordning som omskrivningsregler köra. Den här konfigurationen är användbar när du har flera omskrivningsregler i en omarbetning. En omskrivningsregel som har ett lägre värde för regeln sekvens körs första. Om du tilldelar två omskrivningsregler samma regelsekvens är ordningen för körningen icke-deterministisk.

   - **Skriv om set**: Innehåller flera omskrivningsregler som ska associeras med en regel för vidarebefordran av begäran.

2. Bifoga omskrivning uppsättningen (*rewriteRuleSet*) till en regel för vidarebefordran. Skriv om konfigurationen är kopplat till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När du använder en sökvägsbaserad regel har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. I så fall gäller endast för området angiven sökväg för en plats.

Du kan skapa flera HTTP-huvud omskrivning uppsättningar och tillämpa varje omarbetning som angetts till flera lyssnare. Men du kan använda endast en omarbetning inställd på en viss lyssnare.

## <a name="common-scenarios"></a>Vanliga scenarier

Här följer några vanliga scenarier för att använda rubrik omarbetning.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Ta bort portinformationen från X-vidarebefordrade-för-huvud

Application Gateway infogar ett X-vidarebefordrade-för-huvud i alla begäranden innan den vidarebefordrar begäranden till serverdelen. Den här rubriken är en kommaavgränsad lista över IP-portar. Det kan finnas scenarier där backend servrarna bara behöver rubrikerna som innehåller IP-adresser. Du kan använda huvud-omskrivning för att ta bort portinformationen från X-vidarebefordrade-för-huvud. Ett sätt att göra detta är att ange rubriken till servervariabeln add_x_forwarded_for_proxy:

![Ta bort port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Ändra en omdirigerings-URL

När en server-program skickar ett omdirigering av svar, kanske du vill dirigera om klienten till en annan URL än den som angetts av backend-programmet. Du kanske vill göra det när en app service finns bakom en Programgateway och kräver att klienten att göra en omdirigering till dess relativ sökväg. (Till exempel vara en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2.)

Eftersom App Service är en multitenant-tjänst, använder du värdhuvudet i begäran för att dirigera begäran mot rätt slutpunkt. App services har en standarddomännamnet för *. azurewebsites.net (exempelvis contoso.azurewebsites.net) som skiljer sig från application gateway-domännamn (till exempel contoso.com). Eftersom den ursprungliga begäran från klienten har application-gateway domännamnet (contoso.com) som värdnamnet kan ändras värdnamnet till contoso.azurewebsites.net i application gateway. Det gör den här ändringen så att app service kan vidarebefordra begäran till rätt slutpunkt.

När app service skickar svaret omdirigering, används samma värdnamn i location-huvudet i sitt svar som den i begäran tas emot från programgatewayen. Så kommer klienten se begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå igenom application-gateway (contoso.com/path2). Kringgå application gateway är inte önskvärt.

Du kan lösa det här problemet genom att ange värdnamnet i location-huvudet till application gateway-domännamn.

Här följer stegen för att ersätta värdnamnet:

1. Skapa en omskrivningsregel med ett villkor som utvärderas om location-huvudet i svaret innehåller azurewebsites.net. Ange mönstret `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Utföra en åtgärd för att skriva om location-huvudet så att den har värdnamnet för application gateway. Gör detta genom att ange `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvudets värde.

![Ändra location-huvudet](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementera HTTP-säkerhetshuvuden för att förhindra sårbarheter

Du kan åtgärda säkerhetsproblem i flera genom att implementera nödvändiga rubriker i program-svaret. Dessa säkerhetshuvuden omfattar X XSS skydd och Strict transportsäkerhet innehåll säkerhetsprincip. Du kan använda Application Gateway för att ange dessa huvuden för alla svar.

![Säkerhetshuvudet](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Ta bort oönskade rubriker

Du kanske vill ta bort rubriker som avslöjar känslig information från ett HTTP-svar. Du kanske exempelvis vill ta bort information som backend-servernamnet, operativsystem eller information för bibliotek. Du kan använda application gateway för att ta bort dessa huvuden:

![Ta bort sidhuvud](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Kontrollera om finns en rubrik

Du kan utvärdera en HTTP-begäran eller ett svar huvud för förekomsten av en rubrik eller server variabel. Den här utvärderingsversionen är användbart när du vill utföra en rubrik-omskrivning endast när en viss rubrik finns.

![Kontrollerar förekomsten av en rubrik](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Begränsningar

- Skriva om anslutning, uppgradering och värd-rubriker stöds inte för närvarande.

- Rubriknamn får kan innehålla alla alfanumeriska tecken och specifika symboler som definierats i [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Vi stöder för närvarande inte understreck (\_) specialtecken i rubriknamn.

## <a name="need-help"></a>Behöver du hjälp?

Kontakta oss på [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) om du behöver hjälp med den här funktionen.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skriva om HTTP-huvuden, se:

- [Skriv om HTTP-huvuden med hjälp av Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Skriv om HTTP-huvuden med Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
