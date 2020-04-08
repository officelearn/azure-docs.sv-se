---
title: Azure Application Gateway-funktioner
description: Lär dig mer om Azure Application Gateway-funktioner
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: victorh
ms.openlocfilehash: f021eed959ef88a1ef3671e1d0ace8080710c92a
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810241"
---
# <a name="azure-application-gateway-features"></a>Azure Application Gateway-funktioner

[Azure Application Gateway](overview.md) är en webbtrafikbelastningsutjämningsfaktor som gör att du kan hantera trafik till dina webbprogram.

![Begreppsmässigt om Application Gateway](media/overview/figure1-720.png)

Application Gateway innehåller följande funktioner:

- [SSL/TLS-avslutning (Secure Sockets Layer)](#secure-sockets-layer-ssltls-termination)
- [Automatisk skalning](#autoscaling)
- [Zon redundans](#zone-redundancy)
- [Statisk VIP](#static-vip)
- [Brandvägg för webbaserade program](#web-application-firewall)
- [Ingress-kontrollant för AKS](#ingress-controller-for-aks)
- [URL-baserad routning](#url-based-routing)
- [Värd för flera platser](#multiple-site-hosting)
- [Omdirigering](#redirection)
- [Sessionstillhörighet](#session-affinity)
- [Websocket- och HTTP/2-trafik](#websocket-and-http2-traffic)
- [Anslutningstömning](#connection-draining)
- [Anpassade felsidor](#custom-error-pages)
- [Återskapa HTTP-huvuden](#rewrite-http-headers)
- [Storlekar](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>SSL/TLS-avslutning (Secure Sockets Layer)

Programgateway stöder SSL/TLS-avslutning vid gatewayen, varefter trafiken vanligtvis flödar okrypterad till serverdaservrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. Men ibland okrypterad kommunikation till servrarna är inte ett acceptabelt alternativ. Detta kan bero på säkerhetskrav, efterlevnadskrav eller programmet kan bara acceptera en säker anslutning. För dessa program stöder programgateway slutpunkt till SSL/TLS-kryptering.

Mer information finns i [Översikt över SSL-avslutning och slutpunkt till SSL med Application Gateway](ssl-overview.md)

## <a name="autoscaling"></a>Automatisk skalning

Application Gateway Standard_v2 stöder automatisk skalning och kan skala upp eller ned baserat på förändrade trafikbelastningsmönster. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. 

Mer information om funktionerna för application gateway Standard_v2 finns i [Automatisk skalning v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zon redundans

En Standard_v2 Application Gateway kan sträcka sig över flera tillgänglighetszoner, vilket ger bättre felaktig återhämtning och tar bort behovet av att etablera separata programgateways i varje zon.

## <a name="static-vip"></a>Statisk VIP

Programgatewayen Standard_v2 SKU stöder statisk VIP-typ exklusivt. Detta säkerställer att VIP som är associerat med programgateway inte ändras ens under programgatewayens livstid.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Waf (Web Application Firewall) är en tjänst som ger centraliserat skydd av dina webbprogram från vanliga kryphål och sårbarheter. WAF baseras på regler från [OWASP-kärnregeluppsättningarna](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (endast WAF_v2), 3.0 och 2.2.9. 

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgateways kan enkelt konverteras till en brandvägg för webbprogram.

Mer information finns i [Vad är Azure Web Application Firewall?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Ingress-kontrollant för AKS
Med AGIC (Application Gateway Ingress Controller) kan du använda Application Gateway som inträngning för [aks-kluster (Azure Kubernetes Service).](https://azure.microsoft.com/services/kubernetes-service/) 

Den ingående styrenheten körs som en pod i AKS-klustret och förbrukar [Kubernetes ingress-resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/) och konverterar dem till en Application Gateway-konfiguration, vilket gör att gatewayen kan belastningsbalansera trafik till Kubernetes-poddar. Den ingående styrenheten stöder endast Application Gateway Standard_v2 och WAF_v2 SKU:er. 

Mer information finns i [AGIC (Application Gateway Ingress Controller).](ingress-controller-overview.md)

## <a name="url-based-routing"></a>URL-baserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika pooler.

Till exempel dirigeras begäranden för `http://contoso.com/video/*` till VideoServerPool och `http://contoso.com/images/*` dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

Mer information finns i översikt över baserad routning för [URL-sökväg](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Värd för flera platser

Om du har flera webbplatser så kan du konfigurera fler än en webbplats inom samma programgateway-instans. Med den här funktionen kan du konfigurera en effektivare topologi för dina distributioner genom att lägga till upp till 100 webbplatser i en Application Gateway (för optimal prestanda). Varje webbplats kan dirigeras till en egen programpool. Till exempel kan programgatewayen hantera trafik för `contoso.com` och `fabrikam.com` från två serverpooler som kallas ContosoServerPool och FabrikamServerPool.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara `http://blog.contoso.com` och `http://app.contoso.com` på samma distribution av en programgateway.

Mer information finns i [Programgateway flera webbplatshosting](multiple-site-overview.md).

## <a name="redirection"></a>Omdirigering

Ett vanligt scenario för många webbappar är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan en app och dess användare sker via en krypterad sökväg.

Tidigare kan du ha använt tekniker som dedikerad poolskapande vars enda syfte är att omdirigera begäranden som tas emot på HTTP till HTTPS. Application Gateway stöder möjligheten att omdirigera trafik på programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. Stöd för omdirigering av programgateway är inte begränsat till HTTP till HTTPS-omdirigering ensam. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Den har även stöd för omdirigering till en extern plats.

Stöd för Application Gateway-stöd har följande funktioner:

- Global omdirigering från en port till en annan port på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- Sökvägsbaserad omdirigering. Den här typen av omdirigering möjliggör bara HTTP till HTTPS-omdirigering på ett specifikt webbplatsområde, till exempel en kundvagn som betecknas av `/cart/*`.
- Omdirigera till en extern webbplats.

Mer information finns i [Översikt över omdirigering av Programgateway](redirect-overview.md).

## <a name="session-affinity"></a>Sessionstillhörighet

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

Mer information finns i [Så här fungerar en programgateway](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Websocket- och HTTP/2-trafik

Application Gateway har inbyggt stöd för WebSocket- och HTTP/2-protokoll. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

WebSocket- och HTTP/2-protokollen aktiverar full duplex-kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Det här tillåter en mer interaktiv kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva avsökning som krävs i HTTP-baserade implementeringar. Dessa protokoll har låga omkostnader, till skillnad från HTTP, och kan återanvända samma TCP-anslutning för flera begärande/svar, vilket resulterar i ett effektivare resursutnyttjande. Dessa protokoll är utformade att fungera via de traditionella HTTP-portarna 80 och 443.

Mer information finns i [WebSocket-stöd](application-gateway-websocket.md) och [HTTP/2-stöd](configuration-overview.md#http2-support).

## <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen aktiveras via serverdelens http-inställning och kan tillämpas på alla medlemmar i en serverdelspool i samband med regelskapandet. När den har aktiverats säkerställer Application Gateway att alla avregistreringsinstanser i en serverd-pool inte får någon ny begäran samtidigt som befintliga begäranden kan slutföras inom en konfigurerad tidsgräns. Detta gäller både serverdinstanser som uttryckligen tas bort från serverdapoolen av en användarkonfigurationsändring och serveråtkomstinstanser som rapporteras som felaktiga som bestäms av hälsoavsökningarna. Det enda undantaget till detta är begäranden som är bundna för avregistrering av instanser, som har avregistrerats explicit, på grund av gateway-hanterad sessionstillhörighet och fortsätter att vara proxied till avregistrering instanser.

Mer information finns i [Översikt över konfiguration av programgateway](configuration-overview.md#connection-draining).

## <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

Mer information finns i [Anpassade fel](custom-error.md).

## <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

HTTP-huvuden gör det möjligt för klienten och servern att skicka ytterligare information med begäran eller svaret. Om du skriver om dessa HTTP-huvuden kan du utföra flera viktiga scenarier, till exempel:

- Lägga till säkerhetsrelaterade rubrikfält som HSTS/ X-XSS-Protection.
- Ta bort svarshuvudfält som kan visa känslig information.
- Ta bort portinformation från X-Forwarded-For-huvuden.

Application Gateway stöder möjligheten att lägga till, ta bort eller uppdatera HTTP-begärande- och svarshuvuden, medan begäran- och svarspaketen flyttas mellan klient- och backend-poolerna. Det ger dig också möjlighet att lägga till villkor för att säkerställa att de angivna rubrikerna skrivs om endast när vissa villkor är uppfyllda.

Mer information finns i [Skriva om HTTP-huvuden](rewrite-http-headers.md).

## <a name="sizing"></a>Storlekar

Application Gateway Standard_v2 kan konfigureras för automatisk skalning eller fasta storleksdistributioner. Den här SKU:n erbjuder inte olika instansstorlekar. Mer information om v2-prestanda och priser finns i [Automatisk skalning av v2 SKU](application-gateway-autoscaling-zone-redundant.md#pricing).

Application Gateway Standard erbjuds i tre storlekar: **Small**, **Medium**och **Large**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

I följande tabell visas ett genomsnittligt prestandadataflöde för varje programgateway v1-instans med SSL-avlastning aktiverat:

| Genomsnittligt sidsvarsstorlek för serverdel | Liten | Medel | Stor |
| --- | --- | --- | --- |
| 6 kB |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100 kB |35 Mbit/s |100 Mbps |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

## <a name="version-feature-comparison"></a>Jämförelse av versionsfunktion

En jämförelse av en application gateway v1-v2-funktion finns i [Automatisk skalning och zon redundant Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur Application Gateway fungerar – [hur en programgateway fungerar](how-application-gateway-works.md)