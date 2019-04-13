---
title: Skriv om HTTP-huvuden i Azure Application Gateway | Microsoft Docs
description: Den här artikeln innehåller en översikt över funktionen för att skriva om HTTP-huvuden i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544160"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Skriv om HTTP-huvuden med Application Gateway (offentlig förhandsversion)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med HTTP-huvuden kan klienten och servern skicka ytterligare information med begäran eller svaret. Skriva om dessa HTTP-huvuden kan du utföra flera viktiga scenarier, till exempel att lägga till säkerhetsrelaterade huvudfält som HSTS / X-XSS-skydd, ta bort svarshuvud fält som kan visa känslig information, separeringsapparaten portinformationen från X-vidarebefordrade-för rubriker, osv. Application gateway stöder möjligheten att lägga till, ta bort eller uppdatera HTTP-begäran och svarshuvuden när begäran och svarspaket flytta mellan klienten och serverdelen pooler. Den ger dig också möjligheten att lägga till villkor så att de angivna sidhuvudena är har skrivits bara när vissa villkor uppfylls.
> [!NOTE]
>
> HTTP-huvud omskrivning-stöd är endast tillgänglig för den [nya SKU: N [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

## <a name="headers-supported-for-rewrite"></a>Rubriker som stöds för omarbetning

Funktionen kan du skriva om alla rubriker i begäran och svaret utom rubrikerna som värd, anslutning och uppgradering. Du kan också använda application gateway för att skapa anpassade sidhuvuden och lägga till dem i begäran och svar som dirigeras genom den. 

## <a name="rewrite-conditions"></a>Skriv om villkor

Med hjälp av omarbetning villkor som du kan utvärdera innehållet i HTTP (S)-begäranden och svar och utföra en rubrik omarbetning endast när en eller flera villkor är uppfyllda. Följande 3 typer av variabler som används av application gateway för att utvärdera innehållet i HTTP (S)-begäranden och svar:

- HTTP-huvuden i begäran
- HTTP-huvuden i svaret
- Servervariabler för Application gateway

Ett villkor kan användas för att utvärdera om den angivna variabeln finns, om den angivna variabeln exakt matchar ett specifikt värde eller om den angivna variabeln exakt matchar ett visst mönster. [Perl kompatibla reguljära uttryck (PCRE)-biblioteket](https://www.pcre.org/) används för att implementera mönster för reguljärt uttryck som matchar under förhållanden. Mer information om syntax för reguljära uttryck, finns det [Perl reguljära uttryck man sidan](http://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Skriv om åtgärder

Skriv om åtgärder för att ange de begärande- och svarshuvuden som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till. Du kan antingen skapa ett nytt huvud, ändrar du värdet för en befintlig rubrik eller ta bort en befintlig rubrik. Värdet för ett nytt huvud eller en befintlig rubrik kan anges med följande typer av värden:

- Text 
- Huvudet i begäran: För att ange en rubrik för begäran, måste du använda syntaxen {http_req_*headerName*}
- Svarshuvud: För att ange ett svarshuvud, måste du använda syntaxen {http_resp_*headerName*}
- Servervariabel: För att ange en servervariabel, måste du använda syntaxen {var_*serverVariable*}
- Kombination av text, huvudet i begäran, svarshuvud och en servervariabel.

## <a name="server-variables"></a>Servervariabler

Application gateway använder servervariabler för att lagra användbar information om servern, anslutningen med klienten och den aktuella begäran i anslutningen, till exempel klientens IP-adress eller web webbläsartyp. Dessa variabler ändras dynamiskt, t.ex. när en ny sida läses eller en form publiceras. Du kan använda dessa servervariabler för att utvärdera omskrivning villkor och skriv om rubriker. 

Application gateway stöder följande servervariabler:

| Stöds servervariabler | Beskrivning                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Innehåller ”X-vidarebefordrade-” klienten begäran rubrik fältet för med den `client_ip` (beskrivs i tabellen nedan) variabeln som bifogats i formatet (IP1, IP2, IP3,...). Om fältet ”X-vidarebefordrade-för” inte finns i klienten rubriken, den `add_x_forwarded_for_proxy` variabeln är lika med den `$client_ip` variabeln. Den här variabeln är särskilt användbart i scenarier där kunderna vill skriva om rubriken X-vidarebefordrade-för som Application Gateway, så att huvudet innehåller IP-adress utan portinformation. |
| ciphers_supported          | Returnerar listan över chiffer som stöds av klienten          |
| ciphers_used               | Returnerar en sträng med chiffer som används för en etablerad SSL-anslutning |
| client_ip                  | IP-adressen för klienten som application gateway som tog emot begäran. Om det finns en omvänd proxy innan application gateway och den ursprungliga klienten sedan *client_ip* returnerar IP-adressen för den omvända proxyn. |
| client_port                | klientport                                                  |
| client_tcp_rtt             | information om klienten TCP-anslutning. tillgängligt på system som stöder socketalternativet TCP_INFO |
| client_user                | När du använder HTTP-autentisering, användarnamnet som angetts för autentisering |
| värd                       | i den här rangordning: värdnamn från begäran linje- eller värdnamnet från fältet ”värd” begäran rubrik eller ett servernamn som matchar en begäran |
| cookie_*name*              | den *namn* cookie                                            |
| http_method                | den metod som används för att utföra URL-begäran. Till exempel få POST osv. |
| http_status                | Sessionsstatus, t.ex.: 200, 400, 403 osv.                       |
| http_version               | begäran-protokollet, vanligtvis ”HTTP/1.0”, ”HTTP/1.1” eller ”HTTP/2.0” |
| query_string               | listan över variabeln / värde-par som följer den ””? i den begärda URL: en. |
| received_bytes             | begärandelängd (inklusive raden för kravet, rubrik och begärandetexten) |
| request_query              | argument i raden för kravet                                |
| request_scheme             | begäran-schema, ”http” eller ”https”                            |
| request_uri                | fullständiga ursprungliga begärande-URI (med argument)                   |
| sent_bytes                 | antalet byte som skickats till en klient                             |
| server_port                | port på den server som godkänt en begäran                 |
| ssl_connection_protocol    | Returnerar protokollet för en etablerad SSL-anslutning        |
| ssl_enabled                | ”on” om fungerar anslutningen i SSL-läge eller en tom sträng som på annat sätt |

## <a name="rewrite-configuration"></a>Skriv om konfiguration

För att konfigurera HTTP-huvud omskrivning, måste du:

1. Skapa nya objekt som krävs för att skriva om http-huvuden:

   - **Skriv om åtgärden**: används för att ange begäran och begäran huvudfält som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till. Du kan välja att associera en eller flera omskrivning villkor med en Skriv om åtgärden.

   - **Skriv om villkoret**: Det är en valfri konfiguration. Om ett omskrivning villkor läggs utvärderar det innehållet i HTTP (S)-begäranden och svar. Beslutet att köra åtgärden Skriv om som är associerade med villkoret omskrivning baseras om HTTP (S)-begäran eller svar som matchade med Skriv om villkoret. 

     Om flera villkor som är associerade med en åtgärd och sedan åtgärden körs endast när alla villkor är uppfyllda, d.v.s., ett logiskt AND-åtgärd utförs.

   - **Skriva om regeln**: omskrivningsregel innehåller flera omskrivning åtgärd – Skriv om villkoret kombinationer.

   - **Regeln sekvens**: hjälper till att avgöra den ordning som de olika omarbetning regler körs. Det här är användbart när det finns flera omskrivningsregler i en omarbetning. Omskrivningsregel med mindre regeln Sekvensvärde hämtar köras först. Om du anger samma regelsekvens till två omskrivningsregler kommer ordningen för körningen att icke-deterministisk.

   - **Skriv om Set**: innehåller flera omskrivningsregler som ska kopplas till en regel för vidarebefordran av begäran.

2. Du kommer att behöva koppla omskrivning uppsättningen (*rewriteRuleSet*) med en regel för vidarebefordran. Det beror på att Skriv om konfigurationen är kopplad till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När en sökvägsbaserad regel används har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. Därför gäller detta bara till området angiven sökväg för en plats.

Du kan skapa flera http-huvud omskrivning uppsättningar och varje uppsättning omskrivning kan tillämpas på flera lyssnare. Du kan dock använda endast en omarbetning inställd på en viss lyssnare.

## <a name="common-scenarios"></a>Vanliga scenarier

Några vanliga scenarier som kräver rubrik omskrivning nämns nedan.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Ta bort portinformationen från X-vidarebefordrade-för-huvud

Programgateway infogar rubriken X-vidarebefordrade-för alla begäranden innan den vidarebefordrar begäranden till serverdelen. Formatet för den här rubriken är en kommaavgränsad lista över IP:port. Det kan dock finnas scenarier där backend-servrar kräver rubriken som endast innehåller IP-adresser. För att utföra sådana scenarier användas rubrik omskrivning för att ta bort portinformationen från X-vidarebefordrade-för-huvud. Ett sätt att göra detta är att ange rubriken till add_x_forwarded_for_proxy servervariabel. 

![Ta bort port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Ändra omdirigerings-URL

När en backend-applikationer skickar ett omdirigering av svar, kan du dirigera om klienten till en annan URL än den som angetts av backend-programmet. Ett sådant scenario är när en app service finns bakom en Programgateway och kräver att klienten att göra en omdirigering till dess relativ sökväg (omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2). 

Eftersom app service är en multiklienttjänst, använder du värdhuvudet i begäran för att dirigera till rätt slutpunkt. App services har en standarddomännamnet för *. azurewebsites.net (exempelvis contoso.azurewebsites.net) som skiljer sig från application gateway-domännamn (till exempel contoso.com). Eftersom den ursprungliga begäran från klienten har programgatewayens domännamnet contoso.com som värdnamn, ändringar application gateway värdnamnet för contoso.azurewebsites.net, så att app-tjänsten kan dirigera mot rätt slutpunkt. När app service skickar svaret omdirigering, används samma värdnamn i location-huvudet i sitt svar som den i begäran tas emot från programgatewayen. Därför kan gör klienten begäran direkt till contoso.azurewebsites.net/path2, i stället för att gå igenom application-gateway (contoso.com/path2). Kringgå application gateway är inte önskvärt. 

Det här problemet kan lösas genom att ange värdnamnet i location-huvudet till application gateway-domännamn. Om du vill göra detta måste du skapa en omskrivningsregel med ett villkor som utvärderas om location-huvudet i svaret innehåller azurewebsites.net genom att ange `(https?):\/\/.*azurewebsites\.net(.*)$` som mönstret och utför en åtgärd för att skriva om location-huvudet om du vill ha application gateway värdnamnet genom att ange `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvudets värde.

![Ändra location-huvudet](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementera HTTP-säkerhetshuvuden för att förhindra sårbarheter

Flera säkerhetsproblem kan åtgärdas genom att implementera nödvändiga rubriker i program-svaret. Vissa av dessa säkerhetshuvuden är X XSS skydd, Strict transportsäkerhet, innehåll-säkerhetsprincip osv. Du kan använda application gateway för att ange dessa huvuden för alla svar.

![Säkerhetshuvudet](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>Begränsningar

- Skriva om anslutning, uppgradering och värd-rubriker stöds inte ännu.

- Rubriknamn får innehålla alla alfanumeriska tecken och specifika symboler som definierats i [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Vi för närvarande stöder dock inte ”understreck” (\_) specialtecken i namnet. 

## <a name="need-help"></a>Behöver du hjälp?

Kontakta oss på [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) om du behöver hjälp med den här funktionen.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skriva om HTTP-huvuden, se:

-  [Skriv om HTTP-huvuden med hjälp av Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Skriv om HTTP-huvuden med Azure PowerShell](add-http-header-rewrite-rule-powershell.md)