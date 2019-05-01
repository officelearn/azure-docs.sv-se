---
title: Vad är Azure Application Gateway?
description: Lär dig hur du kan använda Azure Application Gateway för att hantera webbtrafik till din app.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 78dd4b31991a15d3d946c47c5394f64bb3afea95
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947257"
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionella lastbalanserare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port.

![Begreppsmässigt om Application Gateway](media/overview/figure1-720.png)

Du kan fatta beslut om routning baserat på ytterligare attribut för en HTTP-begäran, till exempel URI-sökväg eller värden rubriker med Application Gateway. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. Om `/video` är i Webbadressen som trafik dirigeras till en annan pool som är optimerad för videor.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera.

Följande funktioner ingår med Azure Application Gateway:

## <a name="secure-sockets-layer-ssl-termination"></a>Secure Sockets Layer-avslutning (SSL)

Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdels-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. Men ibland är inte okrypterad kommunikation till servrarna en acceptabel lösning. Det kan bero på säkerhets och kompatibilitetskrav, eller på att programmet bara accepterar en säker anslutning. För de här programmen stöder Application Gateway slutpunkt till slutpunkt-SSL-kryptering.

## <a name="autoscaling"></a>Automatisk skalning

Application Gateway eller WAF-distributioner under Standard_v2 eller WAF_v2 SKU stöder automatisk skalning och kan skala upp eller ned utifrån ändrade trafikmönster belastningen. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Läs mer om Application Gateway standard_v2 och WAF_v2 funktioner [autoskalning v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundans

En Application Gateway eller WAF-distributioner under Standard_v2 eller WAF_v2 SKU kan sträcka sig över flera Tillgänglighetszoner, erbjuder bättre återhämtning för fel och ta bort behovet av att etablera separat Application Gateways i varje zon.

## <a name="static-vip"></a>Statisk VIP

Application gateway VIP på Standard_v2 eller WAF_v2 SKU: N stöder statisk VIP-typ exklusivt. Detta säkerställer att VIP-Adressen som är associerade med application gateway inte ändras även under livslängden för Application Gateway.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brandvägg för webbaserade program (WAF) är en funktion i Application Gateway som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem. WAF baseras på regler från [OWASP-kärnregeluppsättningarna (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. 

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Mer information finns i [Web application firewall (WAF) i Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview)).

## <a name="url-based-routing"></a>URL-baserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika pooler.

Till exempel dirigeras begäranden för `http://contoso.com/video/*` till VideoServerPool och `http://contoso.com/images/*` dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

Mer information finns i [URL-baserad routning med Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Värd för flera platser

Om du har flera webbplatser så kan du konfigurera fler än en webbplats inom samma programgateway-instans. Den här funktionen låter dig konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en programgateway. Varje webbplats kan dirigeras till en egen programpool. Till exempel kan programgatewayen hantera trafik för `contoso.com` och `fabrikam.com` från två serverpooler som kallas ContosoServerPool och FabrikamServerPool.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara `http://blog.contoso.com` och `http://app.contoso.com` på samma distribution av en programgateway.

Mer information finns i [flera-webbplatser med Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Omdirigering

Ett vanligt scenario för många webbappar är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan en app och dess användare sker via en krypterad sökväg.

Tidigare använt tekniker, till exempel skapa dedikerade poolen vars syfte är att omdirigera begäran som tas emot på HTTP till HTTPS. Application Gateway stöder möjligheten att omdirigera trafik på programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. Omdirigering av stöd för Application Gateway är inte begränsad till HTTP till HTTPS-omdirigering enbart. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Den har även stöd för omdirigering till en extern plats.

Stöd för Application Gateway-stöd har följande funktioner:

- Global omdirigering från en port till en annan port på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- Sökvägsbaserad omdirigering. Den här typen av omdirigering möjliggör bara HTTP till HTTPS-omdirigering på ett specifikt webbplatsområde, till exempel en kundvagn som betecknas av `/cart/*`.
- Omdirigera till en extern webbplats.

Mer information finns i [omdirigerar trafik](https://docs.microsoft.com/azure/application-gateway/redirect-overview) med Application Gateway.

## <a name="session-affinity"></a>Sessionstillhörighet

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

## <a name="websocket-and-http2-traffic"></a>Websocket- och HTTP/2-trafik

Application Gateway har inbyggt stöd för WebSocket- och HTTP/2-protokoll. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

WebSocket- och HTTP/2-protokollen aktiverar full duplex-kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Det här tillåter en mer interaktiv kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva avsökning som krävs i HTTP-baserade implementeringar. Dessa protokoll har låg omkostnader, till skillnad från HTTP, och kan återanvända samma TCP-anslutningen för flera begäran/svar vilket resulterar i en mer effektiv resursutnyttjande. Dessa protokoll är utformade att fungera via de traditionella HTTP-portarna 80 och 443.

Mer information finns i [WebSocket-stöd](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) och [stöd för HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Förhandsversion av Azure Kubernetes Service (AKS) Ingress-kontrollant 

Application Gateway Ingress-kontrollanten körs som en pod i AKS-klustret och gör så att Application Gateway kan fungera som ingress för ett AKS-kluster. Detta stöds med Application Gateway v2 endast.

Mer information finns i avsnittet om [Azure Application Gateway Ingress-kontrollant](https://azure.github.io/application-gateway-kubernetes-ingress/).

## <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen aktiveras via serverdelens http-inställning och kan tillämpas på alla medlemmar i en serverdelspool i samband med regelskapandet. När du har aktiverat, säkerställer Application Gateway ta bort registrera instanser av en serverdelspool inte får någon ny begäran samtidigt som befintliga begäranden ska slutföras inom en konfigurerad tid. Detta gäller för både serverdelsinstanser som uttryckligen tas bort från backend-pool med ett API-anrop och serverdelsinstanser som har rapporterats som felaktig systemets hälsoavsökningarna.

## <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

Mer information finns i [skriva om HTTP-huvuden](rewrite-http-headers.md).

## <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

HTTP-huvuden kan klienten och servern att skicka ytterligare information med begäran eller svaret. Skriva om dessa HTTP-huvuden hjälper dig att utföra flera viktiga scenarier, till exempel:

- Lägger till säkerhetsrelaterat namn på huvudfält som HSTS / X-XSS-skydd.
- Ta bort svar huvudfält som kan röja känslig information.
- Tar bort specifika konfigurationer portinformationen från X-vidarebefordrade-för-huvuden.

Application Gateway stöder möjligheten att lägga till, ta bort eller uppdatera HTTP-begäran och svarshuvuden, medan begäran och svar paketen flytta mellan klienten och backend-adresspooler. Det ger dig också möjlighet att lägga till villkor för att se till att de angivna sidhuvudena omskrivningar bara när vissa villkor uppfylls.

Mer information finns i [skriva om HTTP-huvuden](rewrite-http-headers.md).

## <a name="sizing"></a>Storlekar

WAF_v2 SKU: er och Application Gateway Standard_v2 kan konfigureras för automatisk skalning eller fast storlek distributioner. Dessa SKU: er erbjuder inte olika instansstorlekar.

Application Gateway Standard- och WAF SKU finns för närvarande i tre storlekar: **liten**, **medel** och **stor**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Följande tabell visar ett genomsnittligt prestanda-dataflöde för varje Application Gateway-instans som har SSL-avlastning aktiverat:

| Genomsnittligt sidsvarsstorlek för serverdel | Liten | Medel | Stor |
| --- | --- | --- | --- |
| 6 kB |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100 kB |35 Mbit/s |100 Mbit/s |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa en testprogramgateway med Azure Portal, Azure PowerShell eller Azure CLI:

- [Snabbstart: Direkt Internet-trafik med Azure Application Gateway – Azure-portalen](quick-create-portal.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)