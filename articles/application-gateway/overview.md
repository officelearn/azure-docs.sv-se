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
ms.openlocfilehash: a61b1a44419ac35efa5888de2b5a6e4988dfb512
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422308"
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionella lastbalanserare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port.

![Begreppsmässigt om Application Gateway](media/overview/figure1-720.png)

With Application Gateway, you can make routing decisions based on additional attributes of an HTTP request, such as URI path or host headers. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. If `/video` is in the URL, that traffic is routed to another pool that's optimized for videos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. If you need high-performance, low-latency, Layer-4 load balancing, see [What is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) If you're looking for global DNS load balancing, see [What is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Your end-to-end scenarios may benefit from combining these solutions.
>
> For an Azure load-balancing options comparison, see [Overview of load-balancing options in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Följande funktioner ingår med Azure Application Gateway:

## <a name="secure-sockets-layer-ssltls-termination"></a>Secure Sockets Layer (SSL/TLS) termination

Application gateway supports SSL/TLS termination at the gateway, after which traffic typically flows unencrypted to the backend servers. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. Men ibland är inte okrypterad kommunikation till servrarna en acceptabel lösning. This can be because of security requirements, compliance requirements, or the application may only accept a secure connection. For these applications, application gateway supports end to end SSL/TLS encryption.

## <a name="autoscaling"></a>Automatisk skalning

Application Gateway or WAF deployments under Standard_v2 or WAF_v2 SKU support autoscaling and can scale up or down based on changing traffic load patterns. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. For more information about the Application Gateway Standard_v2 and WAF_v2  features, see [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zone redundancy

An Application Gateway or WAF deployments under  Standard_v2 or WAF_v2 SKU can span multiple Availability Zones, offering better fault resiliency and removing the need to provision separate Application Gateways in each zone.

## <a name="static-vip"></a>Static VIP

The application gateway VIP on Standard_v2 or WAF_v2 SKU supports static VIP type exclusively. This ensures that the VIP associated with application gateway doesn't change even over the lifetime of the Application Gateway.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Web application firewall (WAF) is a service that provides centralized protection of your web applications from common exploits and vulnerabilities. WAF is based on rules from the [OWASP (Open Web Application Security Project) core rule sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (WAF_v2 only), 3.0, and 2.2.9. 

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

For more information, see [What is Azure Web Application Firewall?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Ingress-kontrollant för AKS
Application Gateway Ingress Controller (AGIC) allows you to use Application Gateway as the ingress for an [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) cluster. 

The ingress controller runs as a pod within the AKS cluster and consumes [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) and converts them to an Application Gateway configuration which allows the gateway to load-balance traffic to the Kubernetes pods. The ingress controller only supports Application Gateway V2 SKU. 

For more information, see [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-baserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika pooler.

Till exempel dirigeras begäranden för `http://contoso.com/video/*` till VideoServerPool och `http://contoso.com/images/*` dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

For more information, see [URL-based routing with Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Värd för flera platser

Om du har flera webbplatser så kan du konfigurera fler än en webbplats inom samma programgateway-instans. This feature allows you to configure a more efficient topology for your deployments by adding up to 100 web sites to one Application Gateway, or 40 for WAF (for optimal performance). Varje webbplats kan dirigeras till en egen programpool. Till exempel kan programgatewayen hantera trafik för `contoso.com` och `fabrikam.com` från två serverpooler som kallas ContosoServerPool och FabrikamServerPool.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara `http://blog.contoso.com` och `http://app.contoso.com` på samma distribution av en programgateway.

For more information, see [multiple-site hosting with Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Omdirigering

Ett vanligt scenario för många webbappar är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan en app och dess användare sker via en krypterad sökväg.

In the past, you may have used techniques such as dedicated pool creation whose sole purpose is to redirect requests it receives on HTTP to HTTPS. Application Gateway stöder möjligheten att omdirigera trafik på programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. Application Gateway redirection support isn't limited to HTTP to HTTPS redirection alone. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Den har även stöd för omdirigering till en extern plats.

Stöd för Application Gateway-stöd har följande funktioner:

- Global omdirigering från en port till en annan port på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- Sökvägsbaserad omdirigering. Den här typen av omdirigering möjliggör bara HTTP till HTTPS-omdirigering på ett specifikt webbplatsområde, till exempel en kundvagn som betecknas av `/cart/*`.
- Omdirigera till en extern webbplats.

For more information, see [redirecting traffic](https://docs.microsoft.com/azure/application-gateway/redirect-overview) with Application Gateway.

## <a name="session-affinity"></a>Sessionstillhörighet

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

## <a name="websocket-and-http2-traffic"></a>Websocket- och HTTP/2-trafik

Application Gateway har inbyggt stöd för WebSocket- och HTTP/2-protokoll. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

WebSocket- och HTTP/2-protokollen aktiverar full duplex-kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Det här tillåter en mer interaktiv kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva avsökning som krävs i HTTP-baserade implementeringar. These protocols have low overhead, unlike HTTP, and can reuse the same TCP connection for multiple request/responses resulting in a more efficient resource utilization . Dessa protokoll är utformade att fungera via de traditionella HTTP-portarna 80 och 443.

For more information, see [WebSocket support](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) and [HTTP/2 support](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen aktiveras via serverdelens http-inställning och kan tillämpas på alla medlemmar i en serverdelspool i samband med regelskapandet. Once enabled, Application Gateway ensures all de-registering instances of a backend pool do not receive any new request while allowing existing requests to complete within a configured time limit. This applies to both backend instances that are explicitly removed from the backend pool by an API call, and backend instances that are reported as unhealthy as determined by the health probes.

For more information, see the Connection Draining section of [Application Gateway Configuration Overview](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

For more information, see [Custom Errors](custom-error.md).

## <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

HTTP headers allow the client and server to pass additional information with the request or the response. Rewriting these HTTP headers helps you accomplish several important scenarios, such as:

- Adding security-related header fields like HSTS/ X-XSS-Protection.
- Removing response header fields that can reveal sensitive information.
- Stripping port information from X-Forwarded-For headers.

Application Gateway supports the capability to add, remove, or update HTTP request and response headers, while the request and response packets move between the client and back-end pools. It also provides you with the capability to add conditions to ensure the specified headers are rewritten only when certain conditions are met.

For more information, see [Rewrite HTTP headers](rewrite-http-headers.md).

## <a name="sizing"></a>Storlekar

Application Gateway Standard_v2 and WAF_v2 SKU can be configured for autoscaling or fixed size deployments. These SKUs don't offer different instance sizes. For more information on v2 performance and pricing, see [Autoscaling v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

The Application Gateway Standard and WAF SKU is currently offered in three sizes: **Small**, **Medium**, and **Large**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

The following table shows an average performance throughput for each application gateway v1 instance with SSL offload enabled:

| Genomsnittligt sidsvarsstorlek för serverdel | Liten | Medium | Stor |
| --- | --- | --- | --- |
| 6 kB |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100 kB |35 Mbit/s |100 Mbit/s |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa en testprogramgateway med Azure Portal, Azure PowerShell eller Azure CLI:

- [Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](quick-create-portal.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)
