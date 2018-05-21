---
title: Vad är Azure Application Gateway?
description: Lär dig hur du kan använda Azure Application Gateway för att hantera webbtrafik till din app.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.workload: infrastructure-services
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 045443637c06745472458dd9e33670875a33352b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en belastningsutjämnare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. 

Traditionella belastningsutjämnare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port. Men med Application Gateway kan du vara ännu mer specifik. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. Om `/video` finns i URL:en dirigeras trafiken dirigeras till en annan adresspool som har optimerats för videor.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera. 

Följande funktioner ingår med Azure Application Gateway: 

## <a name="secure-sockets-layer-ssl-termination"></a>Secure Sockets Layer-avslutning (SSL)

Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdels-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. Men ibland är inte okrypterad kommunikation till servrarna en acceptabel lösning. Det kan bero på säkerhetskrav eller efterföljandekrav eller att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brandvägg för webbaserade program (WAF) är en funktion i Application Gateway som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem. WAF baseras på regler från [OWASP-kärnregeluppsättningarna (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. 

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

## <a name="url-based-routing"></a>URL-baserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika pooler.

Till exempel dirigeras begäranden för `http://contoso.com/video/*` till VideoServerPool och `http://contoso.com/images/*` dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

## <a name="multiple-site-hosting"></a>Värd för flera platser

Om du har flera webbplatser så kan du konfigurera fler än en webbplats inom samma programgateway-instans. Den här funktionen låter dig konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 20 webbplatser till en programgateway. Varje webbplats kan dirigeras till en egen programpool. Till exempel kan programgatewayen hantera trafik för `contoso.com` och `fabrikam.com` från två serverpooler som kallas ContosoServerPool och FabrikamServerPool.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara `http://blog.contoso.com` och `http://app.contoso.com` på samma distribution av en programgateway.

## <a name="redirection"></a>Omdirigering

Ett vanligt scenario för många webbappar är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan en app och dess användare sker via en krypterad sökväg. 

Tidigare kan du har använt tekniker som att skapa en dedikerad pool vars syfte är att omdirigera begäranden den tar emot via HTTP till HTTPS. Application Gateway stöder möjligheten att omdirigera trafik på programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. Stöd för Application Gateway-omdirigering är inte begränsat till enbart HTTPS till HTTP-omdirigering. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Den har även stöd för omdirigering till en extern plats.

Stöd för Application Gateway-stöd har följande funktioner:

- Global omdirigering från en port till en annan port på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- Sökvägsbaserad omdirigering. Den här typen av omdirigering möjliggör bara HTTP till HTTPS-omdirigering på ett specifikt webbplatsområde, till exempel en kundvagn som betecknas av `/cart/*`.
- Omdirigera till en extern webbplats.



## <a name="session-affinity"></a>Sessionstillhörighet

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.




## <a name="websocket-and-http2-traffic"></a>Websocket- och HTTP/2-trafik

Application Gateway har inbyggt stöd för WebSocket- och HTTP/2-protokoll. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. Stöd för HTTP-2 kan aktiveras med hjälp av Azure PowerShell.
 
WebSocket- och HTTP/2-protokollen aktiverar full duplex-kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Det här tillåter en mer interaktiv kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva avsökning som krävs i HTTP-baserade implementeringar. Dessa protokoll har låg omkostnader, till skillnad från HTTP, och kan återanvända samma TCP-anslutning för flera begäranden/svar, vilket resulterar i ett mer effektivt utnyttjande av resurser. Dessa protokoll är utformade att fungera via de traditionella HTTP-portarna 80 och 443.



## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa en testprogramgateway med Azure Portal, Azure PowerShell eller Azure CLI:

- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal](quick-create-portal.md).
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)
