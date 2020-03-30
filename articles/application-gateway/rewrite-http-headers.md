---
title: Skriv om HTTP-huvuden med Azure Application Gateway | Microsoft-dokument
description: Den här artikeln innehåller en översikt över hur HTTP-huvuden skrivs om i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133000"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Skriva om HTTP-huvuden med Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-huvuden gör det möjligt för en klient och server att skicka ytterligare information med en begäran eller ett svar. Genom att skriva om dessa rubriker kan du utföra viktiga uppgifter, till exempel lägga till säkerhetsrelaterade rubrikfält som HSTS/ X-XSS-Protection, ta bort svarshuvudfält som kan visa känslig information och ta bort portinformation från X-Forwarded-For-huvuden.

Med Application Gateway kan du lägga till, ta bort eller uppdatera HTTP-huvuden för begäran och svar när begäran- och svarspaketen flyttas mellan klientens och serverdelens pooler. Du kan också lägga till villkor så att de angivna rubrikerna bara skrivs om när vissa villkor uppfylls.

Application Gateway stöder också flera [servervariabler](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) som hjälper dig att lagra ytterligare information om begäranden och svar. Detta gör det lättare för dig att skapa kraftfulla omskrivningsregler.

> [!NOTE]
>
> Stöd för HTTP-huvud skriva om är endast tillgängligt för [Standard_V2 och WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Skriva om rubriker](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Rubriker som stöds

Du kan skriva om alla rubriker i begäranden och svar, förutom rubrikerna Värd, Anslutning och Uppgradera. Du kan också använda programgatewayen för att skapa anpassade rubriker och lägga till dem i begäranden och svar som dirigeras genom den.

## <a name="rewrite-conditions"></a>Skriv om villkor

Du kan använda omskrivningsvillkor för att utvärdera innehållet i HTTP(S) begäranden och svar och utföra en rubrikändring endast när ett eller flera villkor är uppfyllda. Programgatewayen använder dessa typer av variabler för att utvärdera innehållet i HTTP(S) begäranden och svar:

- HTTP-huvuden i begäran.
- HTTP-huvuden i svaret.
- Servervariabler för Program gateway.

Du kan använda ett villkor för att utvärdera om det finns en angiven variabel, om en angiven variabel matchar ett visst värde eller om en angiven variabel matchar ett visst mönster. Du kan använda [pcre-biblioteket (Perl Compatible Regular Expressions)](https://www.pcre.org/) för att ställa in mönstermatchning av reguljära uttryck i villkoren. Mer information om syntax för reguljära uttryck finns på huvudsidan för [Perl reguljära uttryck](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Skriva om åtgärder

Du kan skriva om åtgärder för att ange de begäran- och svarsrubriker som du vill skriva om och det nya värdet för rubrikerna. Du kan antingen skapa ett nytt huvud, ändra värdet för ett befintligt huvud eller ta bort ett befintligt huvud. Värdet för ett nytt huvud eller ett befintligt huvud kan ställas in på följande typer av värden:

- Text.
- Begär rubrik. Om du vill ange ett begärandehuvud måste du använda syntaxen {http_req_*headerName*}.
- Svarshuvud. Om du vill ange ett svarshuvud måste du använda syntaxen {http_resp_*headerName*}.
- Servervariabel. Om du vill ange en servervariabel måste du använda syntaxen {var_*serverVariable*}.
- En kombination av text, ett begärandehuvud, ett svarshuvud och en servervariabel.

## <a name="server-variables"></a>Servervariabler

Application Gateway använder servervariabler för att lagra användbar information om servern, anslutningen till klienten och den aktuella begäran på anslutningen. Exempel på information som lagras är klientens IP-adress och webbläsartypen. Servervariabler ändras dynamiskt, till exempel när en ny sida läses in eller när ett formulär bokförs. Du kan använda dessa variabler för att utvärdera omskrivningsvillkor och skriva om rubriker.

Programgateway stöder dessa servervariabler:

| Variabelnamn | Beskrivning                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Fältet X-Forwarded-For-klientbegäran med `client_ip` variabeln (se förklaring senare i den här tabellen) läggs till i formatet IP1, IP2, IP3 och så vidare. Om fältet X-Vidarebefordrad för inte finns i klientbegäranden är variabeln `add_x_forwarded_for_proxy` lika med variabeln. `$client_ip` Den här variabeln är särskilt användbar när du vill skriva om X-Forwarded-For-huvudet som angetts av Application Gateway så att huvudet bara innehåller IP-adressen utan portinformation. |
| ciphers_supported          | En lista över de chiffer som stöds av klienten.          |
| ciphers_used               | Den sträng med chiffer som används för en etablerad TLS-anslutning. |
| client_ip                  | IP-adressen för klienten som programgatewayen tog emot begäran från. Om det finns en omvänd proxy innan programgatewayen och den ursprungliga klienten returnerar *client_ip* IP-adressen för den omvända proxyn. |
| client_port                | Klientporten.                                                  |
| client_tcp_rtt             | Information om klient-TCP-anslutningen. Finns på system som stöder TCP_INFO socketalternativet. |
| client_user                | När HTTP-autentisering används anges användarnamnet för autentisering. |
| värd                       | I den här prioritetsordningen: värdnamnet från förfråligensraden, värdnamnet från fältet Värdbegäran eller servernamnet som matchar en begäran. |
| cookie_*namn*              | *Namnet* cookie.                                            |
| http_method                | Den metod som används för att göra URL-begäran. Till exempel GET eller POST. |
| http_status                | Sessionsstatus. Till exempel 200, 400 eller 403.                       |
| http_version               | Protokollet för begäran. Vanligtvis HTTP/1.0, HTTP/1.1 eller HTTP/2.0. |
| query_string               | Listan över variabel-/värdepar som följer "?" i den begärda webbadressen. |
| received_bytes             | Längden på begäran (inklusive förfrådetexten, huvudet och begäranden). |
| request_query              | Argumenten på raden för begäran.                                |
| request_scheme             | Systemet för begäran: http eller https.                            |
| request_uri                | Den fullständiga ursprungliga begäran URI (med argument).                   |
| sent_bytes                 | Antalet byte som skickas till en klient.                             |
| server_port                | Porten på servern som accepterade en begäran.                 |
| ssl_connection_protocol    | Protokollet för en etablerad TLS-anslutning.        |
| ssl_enabled                | "På" om anslutningen fungerar i TLS-läge. Annars en tom sträng. |

## <a name="rewrite-configuration"></a>Skriv om konfiguration

Om du vill konfigurera OMskrivning av HTTP-huvuden måste du utföra de här stegen.

1. Skapa de objekt som krävs för HTTP-huvud skriva om:

   - **Skriv om åtgärd:** Används för att ange de fält för begäran och begäran om huvud som du vill skriva om och det nya värdet för rubrikerna. Du kan associera ett eller flera omskrivningsvillkor med en omskrivningsåtgärd.

   - **Skriv om villkor**: En valfri konfiguration. Omskrivningsvillkor utvärderar innehållet i HTTP(S) begäranden och svar. Omskrivningsåtgärden inträffar om HTTP(S)-begäran eller -svaret matchar omskrivningsvillkoret.

     Om du associerar mer än ett villkor med en åtgärd inträffar åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden en logisk OCH-åtgärd.

   - **Skriv om regel**: Innehåller flera omskrivningsåtgärder/skriva om villkorskombinationer.

   - **Regelsekvens**: Hjälper till att bestämma i vilken ordning omskrivningsreglerna ska köras. Den här konfigurationen är användbar när du har flera omskrivningsregler i en omskrivningsuppsättning. En omskrivningsregel som har ett lägre regelsekvensvärde körs först. Om du tilldelar samma regelsekvens till två omskrivningsregler är körningsordningen icke-deterministisk.

   - **Skriv om set**: Innehåller flera omskrivningsregler som ska associeras med en routningsregel för begäran.

2. Koppla omskrivningsuppsättningen *(skriv omRuleSet)* till en routningsregel. Omskrivningskonfigurationen är kopplad till källavlyssningsaren via routningsregeln. När du använder en grundläggande routningsregel associeras konfigurationen för huvudreformat med en källlyssnare och en global rubrikskrivning. När du använder en sökvägsbaserad routningsregel definieras konfigurationen för rubrikreda på URL-sökvägskartan. I så fall gäller det endast för området för en platss specifika sökväg.
   > [!NOTE]
   > URL Skriv om ändra rubrikerna; Den ändrar inte URL:en för sökvägen.

Du kan skapa flera HTTP-rubrikstrevuppsättningar och använda varje omskrivningsuppsättning på flera lyssnare. Men du kan bara använda en omskrivningsuppsättning på en viss lyssnare.

## <a name="common-scenarios"></a>Vanliga scenarier

Här är några vanliga scenarier för att använda rubrik skriva om.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Ta bort portinformation från X-Forwarded-For-huvudet

Application Gateway infogar en X-Forwarded-For-huvud i alla begäranden innan begäranden vidarebefordras till backend. Det här huvudet är en kommaavgränsad lista över IP-portar. Det kan finnas scenarier där backend-servrarna bara behöver rubrikerna för att innehålla IP-adresser. Du kan använda rubrikrewrite för att ta bort portinformationen från X-Forwarded-For-huvudet. Ett sätt att göra detta är att ställa in huvudet på add_x_forwarded_for_proxy servervariabel:

![Ta bort port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Ändra url för omdirigering

När ett backend-program skickar ett omdirigeringssvar kanske du vill omdirigera klienten till en annan URL än den som anges av backend-programmet. Du kanske till exempel vill göra detta när en apptjänst finns bakom en programgateway och kräver att klienten gör en omdirigering till dess relativa sökväg. (Till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2.)

Eftersom App Service är en tjänst med flera trogna används värdhuvudet i begäran för att dirigera begäran till rätt slutpunkt. Apptjänster har ett standarddomännamn på *.azurewebsites.net (säg contoso.azurewebsites.net) som skiljer sig från programgatewayens domännamn (säg contoso.com). Eftersom den ursprungliga begäran från klienten har programgatewayens domännamn (contoso.com) som värdnamn, ändrar programgatewayen värdnamnet till contoso.azurewebsites.net. Den här ändringen så att apptjänsten kan dirigera begäran till rätt slutpunkt.

När apptjänsten skickar ett omdirigeringssvar används samma värdnamn i platshuvudet för sitt svar som det i begäran som den tar emot från programgatewayen. Så klienten kommer att göra begäran direkt till contoso.azurewebsites.net/path2 istället för att gå igenom programmet gateway (contoso.com/path2). Att kringgå programgatewayen är inte önskvärt.

Du kan lösa problemet genom att ange värdnamnet i platshuvudet till programgatewayens domännamn.

Här är stegen för att ersätta värdnamnet:

1. Skapa en skrivregel med ett villkor som utvärderar om platshuvudet i svaret innehåller azurewebsites.net. Ange mönstret `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Utför en åtgärd för att skriva om platshuvudet så att programgatewayens värdnamn har. Gör detta genom `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` att ange som huvudvärde.

![Ändra platshuvud](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementera HTTP-säkerhetshuvuden för att förhindra säkerhetsproblem

Du kan åtgärda flera säkerhetsproblem genom att implementera nödvändiga rubriker i programsvaret. Dessa säkerhetsrubriker inkluderar X-XSS-Protection, Strict-Transport-Security och Content-Security-Policy. Du kan använda Application Gateway för att ange dessa rubriker för alla svar.

![Säkerhetshuvud](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Ta bort oönskade rubriker

Du kanske vill ta bort rubriker som visar känslig information från ett HTTP-svar. Du kanske till exempel vill ta bort information som servernamn, operativsystem eller biblioteksinformation. Du kan använda programgatewayen för att ta bort dessa rubriker:

![Ta bort rubrik](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Kontrollera om det finns ett sidhuvud

Du kan utvärdera en HTTP-begäran eller svarshuvud för förekomsten av en rubrik eller servervariabel. Den här utvärderingen är användbar när du bara vill skriva om ett sidhuvud när det finns ett visst huvud.

![Kontrollera förekomsten av ett sidhuvud](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Begränsningar

- Om ett svar har mer än ett sidhuvud med samma namn, kommer omskrivningen värdet för ett av dessa rubriker att resultera i att de andra rubrikerna släpps i svaret. Detta kan vanligtvis hända med Set-Cookie-huvudet eftersom du kan ha mer än en Set-Cookie-rubrik i ett svar. Ett sådant scenario är när du använder en apptjänst med en programgateway och har konfigurerat cookie-baserad sessionstillhörighet på programgatewayen. I det här fallet kommer svaret att innehålla två Set-Cookie-huvuden: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` en som används av apptjänsten, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`till exempel: och en annan för programgatewaytillhörighet, till exempel . Om du skriver om en av set-cookie-huvudena i det här scenariot kan det leda till att den andra Set-Cookie-huvudet tas bort från svaret.

- Det stöds för närvarande inte att skriva om rubrikerna Anslutning, Uppgradering och Värd.

- Rubriknamn kan innehålla alla alfanumeriska tecken och specifika symboler enligt definitionen i [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Vi stöder för närvarande inte\_understrecket ( ) specialtecken i Sidhuvudnamn.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skriver om HTTP-huvuden finns i:

- [Skriva om HTTP-huvuden med Azure-portalen](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Skriva om HTTP-huvuden med Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
