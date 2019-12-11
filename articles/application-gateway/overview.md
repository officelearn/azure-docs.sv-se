---
title: Vad är Azure Application Gateway?
description: Lär dig hur du kan använda Azure Application Gateway för att hantera webbtrafik till din app.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: 29962fa217c34088ed17fdea68c2c1189a3bfcd2
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996586"
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionella lastbalanserare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port.

![Begreppsmässigt om Application Gateway](media/overview/figure1-720.png)

Med Application Gateway kan du fatta beslut om routning baserat på ytterligare attribut för en HTTP-begäran, till exempel URI-sökväg eller värd rubriker. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. Om `/video` finns i URL dirigeras trafiken till en annan pool som är optimerad för videor.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du behöver hög prestanda, låg latens, Layer-4 belastnings utjämning, se [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Om du letar efter global belastnings utjämning för DNS, se [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera dessa lösningar.
>
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Följande funktioner ingår med Azure Application Gateway:

## <a name="secure-sockets-layer-ssltls-termination"></a>Secure Sockets Layer (SSL/TLS) avslutning

Application Gateway stöder SSL/TLS-avslutning på gatewayen, efter vilken trafiken vanligt vis flödar okrypterad till backend-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. Men ibland är inte okrypterad kommunikation till servrarna en acceptabel lösning. Detta kan bero på säkerhets krav, krav på efterlevnad eller att programmet bara kan godkänna en säker anslutning. Programgatewayen stöder SSL/TLS-kryptering från slut punkt till slut punkt för dessa program.

## <a name="autoscaling"></a>Automatisk skalning

Application Gateway-eller WAF-distributioner under Standard_v2 eller WAF_v2 SKU: n stöder automatisk skalning och kan skalas upp eller ned baserat på ändrade trafik belastnings mönster. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Mer information om Application Gateway Standard_v2-och WAF_v2-funktioner finns i [autoskalning v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zon redundans

En Application Gateway-eller WAF-distribution under Standard_v2 eller WAF_v2 SKU kan sträcka sig över flera Tillgänglighetszoner, erbjuda bättre fel återhämtning och ta bort behovet av att etablera separata programgatewayer i varje zon.

## <a name="static-vip"></a>Statisk VIP

Programgatewayens VIP på Standard_v2 eller WAF_v2 SKU stöder enbart statisk VIP-typ. Detta säkerställer att den VIP som är associerad med Application Gateway inte ändras ens under Application Gatewayens livstid.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brand vägg för webbaserade program (WAF) är en tjänst som tillhandahåller centraliserat skydd av dina webb program från vanliga sårbarheter och sårbarheter. WAF baseras på regler från [OWASP (Open Web Application Security Project) Core Rule set](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (endast WAF_v2), 3,0 och 2.2.9. 

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Mer information finns i [Vad är en brand vägg för Azure Web Application?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Ingress-kontrollant för AKS
Med Application Gateway ingångs kontroll (AGIC) kan du använda Application Gateway som ingångs punkt för ett [Azure Kubernetes service-kluster (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . 

Ingångs kontrollen körs som en POD i AKS-klustret och använder [Kubernetes ingångs resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/) och konverterar dem till en Application Gateway-konfiguration som gör att gatewayen kan belastningsutjämna trafik till Kubernetes poddar. Ingångs styrenheten har endast stöd för Application Gateway v2 SKU. 

Mer information finns i [Application Gateway ingress-styrenhet (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-baserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika pooler.

Till exempel dirigeras begäranden för `http://contoso.com/video/*` till VideoServerPool och `http://contoso.com/images/*` dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

Mer information finns i [URL-baserad routning med Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Värd för flera platser

Om du har flera webbplatser så kan du konfigurera fler än en webbplats inom samma programgateway-instans. Med den här funktionen kan du konfigurera en effektivare topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en Application Gateway eller 40 för WAF (för optimala prestanda). Varje webbplats kan dirigeras till en egen programpool. Till exempel kan programgatewayen hantera trafik för `contoso.com` och `fabrikam.com` från två serverpooler som kallas ContosoServerPool och FabrikamServerPool.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara `http://blog.contoso.com` och `http://app.contoso.com` på samma distribution av en programgateway.

Mer information finns i [värd tjänster för flera platser med Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Omdirigering

Ett vanligt scenario för många webbappar är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan en app och dess användare sker via en krypterad sökväg.

Tidigare kan du ha använt tekniker, till exempel att en dedikerad pool ska skapas vars enda syfte är att omdirigera begär Anden som tas emot på HTTP till HTTPS. Application Gateway stöder möjligheten att omdirigera trafik på programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. Stöd för Application Gateway omdirigering är inte begränsat till enbart HTTP till HTTPS-omdirigering. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Den har även stöd för omdirigering till en extern plats.

Stöd för Application Gateway-stöd har följande funktioner:

- Global omdirigering från en port till en annan port på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- Sökvägsbaserad omdirigering. Den här typen av omdirigering möjliggör bara HTTP till HTTPS-omdirigering på ett specifikt webbplatsområde, till exempel en kundvagn som betecknas av `/cart/*`.
- Omdirigera till en extern webbplats.

Mer information finns i [omdirigera trafik](https://docs.microsoft.com/azure/application-gateway/redirect-overview) med Application Gateway.

## <a name="session-affinity"></a>Sessionstillhörighet

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

## <a name="websocket-and-http2-traffic"></a>Websocket- och HTTP/2-trafik

Application Gateway har inbyggt stöd för WebSocket- och HTTP/2-protokoll. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

WebSocket- och HTTP/2-protokollen aktiverar full duplex-kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Det här tillåter en mer interaktiv kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva avsökning som krävs i HTTP-baserade implementeringar. Dessa protokoll har låg belastning, till skillnad från HTTP, och kan återanvända samma TCP-anslutning för flera begär Anden/svar som resulterar i en mer effektiv resursutnyttjande. Dessa protokoll är utformade att fungera via de traditionella HTTP-portarna 80 och 443.

Mer information finns i stöd för [WebSocket-support](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) och [http/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen aktiveras via serverdelens http-inställning och kan tillämpas på alla medlemmar i en serverdelspool i samband med regelskapandet. Application Gateway säkerställer att alla avregistrera instanser av en backend-pool inte får någon ny begäran samtidigt som befintliga begär Anden kan slutföras inom en konfigurerad tids gräns. Detta gäller för båda Server dels instanserna som uttryckligen tas bort från backend-poolen av en användar konfigurations ändring och Server dels instanser som rapporteras som ohälsosama enligt vad som fastställs av hälso avsökningarna. Det enda undantaget till detta är begär Anden som är kopplade till avregistrering av instanser, som har avregistrerats uttryckligen, på grund av en Gateway-hanterad session tillhörighet och kommer att fortsätta att vara proxy till de deregistrerade instanserna.

Mer information finns i avsnittet om tömning av anslutningar i [Application Gateway konfigurations översikt](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

Mer information finns i [anpassade fel](custom-error.md).

## <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

HTTP-huvuden gör att klienten och servern kan skicka ytterligare information med begäran eller svaret. Genom att skriva om dessa HTTP-huvuden kan du utföra flera viktiga scenarier, t. ex.:

- Lägga till säkerhetsrelaterade huvud fält som HSTS/X-XSS-Protection.
- Tar bort fält för svars huvud som kan avslöja känslig information.
- Tar fram portinformation från X-vidarebefordrade – för meddelandehuvuden.

Application Gateway stöder möjligheten att lägga till, ta bort eller uppdatera HTTP-begäran och svarshuvuden, medan paket för begäran och svar flyttas mellan klienten och backend-pooler. Du får också möjlighet att lägga till villkor för att se till att de angivna rubrikerna bara skrivs om när vissa villkor är uppfyllda.

Mer information finns i [skriva om HTTP-huvuden](rewrite-http-headers.md).

## <a name="sizing"></a>Storlekar

Application Gateway Standard_v2 och WAF_v2 SKU kan konfigureras för automatisk skalning eller distributioner med fast storlek. Dessa SKU: er erbjuder inte olika instans storlekar. Mer information om v2-prestanda och priser finns i [autoskalning v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

Application Gateway standard-och WAF SKU finns för närvarande i tre storlekar: **liten**, **medel**och **stor**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

I följande tabell visas ett genomsnittligt prestanda data flöde för varje Application Gateway v1-instans med SSL-avlastning aktive rad:

| Genomsnittligt sidsvarsstorlek för serverdel | Liten | Medium | Stor |
| --- | --- | --- | --- |
| 6 kB |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100 kB |35 Mbit/s |100 Mbit/s |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa en testprogramgateway med Azure Portal, Azure PowerShell eller Azure CLI:

- [Snabb start: direkt webb trafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)
