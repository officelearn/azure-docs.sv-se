---
title: Metodtips för operatör – Nätverksanslutning i Azure Kubernetes Services (AKS)
description: Lär dig metodtips för klusteroperatören för virtuella nätverksresurser och anslutning i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 93659a0891b09c83db9f63fe0756fcf4d7e87f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594693"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Metodtips för nätverksanslutning och säkerhet i Azure Kubernetes Service (AKS)

När du skapar och hanterar kluster i Azure Kubernetes Service (AKS) tillhandahåller du nätverksanslutning för dina noder och program. Dessa nätverksresurser omfattar IP-adressintervall, belastningsutjämnare och ingående styrenheter. För att upprätthålla en hög servicekvalitet för dina program måste du planera för och sedan konfigurera dessa resurser.

Den här artikeln om metodtips fokuserar på nätverksanslutning och säkerhet för klusteroperatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Jämföra kubenet- och Azure CNI-nätverkslägen i AKS
> * Planera för nödvändig IP-adressering och anslutning
> * Fördela trafik med belastningsutjämnare, ingående styrenheter eller en brandvägg för webbprogram (WAF)
> * Anslut till klusternoder på ett säkert sätt

## <a name="choose-the-appropriate-network-model"></a>Välj lämplig nätverksmodell

**Vägledning för bästa praxis** – För integrering med befintliga virtuella nätverk eller lokala nätverk använder du Azure CNI-nätverk i AKS. Den här nätverksmodellen möjliggör också större separation av resurser och kontroller i en företagsmiljö.

Virtuella nätverk ger grundläggande anslutning för AKS-noder och kunder för att komma åt dina program. Det finns två olika sätt att distribuera AKS-kluster till virtuella nätverk:

* **Kubenet-nätverk** – Azure hanterar de virtuella nätverksresurserna när klustret distribueras och använder plugin-programmet [kubenet][kubenet] Kubernetes.
* **Azure CNI-nätverk** – distribueras till ett befintligt virtuellt nätverk och använder [inspelet CNI (Azure Container Networking Interface)][cni-networking] Kubernetes. Poddar får enskilda IPs som kan dirigeras till andra nätverkstjänster eller lokala resurser.

CNI (Container Networking Interface) är ett leverantörsneutralt protokoll som gör att behållarkörningen kan göra begäranden till en nätverksprovider. Azure CNI tilldelar IP-adresser till poddar och noder och tillhandahåller IP-adresshanteringsfunktioner (IPAM) när du ansluter till befintliga virtuella Azure-nätverk. Varje nod- och pod-resurs tar emot en IP-adress i det virtuella Azure-nätverket och ingen ytterligare routning behövs för att kommunicera med andra resurser eller tjänster.

![Diagram som visar två noder med bryggor som ansluter var och en till ett enda Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

För de flesta produktionsdistributioner bör du använda Azure CNI-nätverk. Denna nätverksmodell möjliggör separation av kontroll och hantering av resurser. Ur ett säkerhetsperspektiv vill du ofta att olika team ska hantera och skydda dessa resurser. Med Azure CNI-nätverk kan du ansluta till befintliga Azure-resurser, lokala resurser eller andra tjänster direkt via IP-adresser som tilldelats varje pod.

När du använder Azure CNI-nätverk finns den virtuella nätverksresursen i en separat resursgrupp till AKS-klustret. Delegera behörigheter för AKS-tjänstens huvudnamn för åtkomst och hantering av dessa resurser. Tjänsthuvudhuvudnamnet som används av AKS-klustret måste ha minst [behörigheten Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor) i undernätet i det virtuella nätverket. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda rollen inbyggd nätverksdeltagare krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Mer information om aks-tjänstens huvuddelegering finns i [Delegera åtkomst till andra Azure-resurser][sp-delegation].

När varje nod och pod får sin egen IP-adress, planera adressintervallen för AKS-undernäten. Undernätet måste vara tillräckligt stort för att tillhandahålla IP-adresser för varje nod, poddar och nätverksresurser som du distribuerar. Varje AKS-kluster måste placeras i sitt eget undernät. Om du vill tillåta anslutning till lokala eller peer-nätverk i Azure ska du inte använda IP-adressintervall som överlappar befintliga nätverksresurser. Det finns standardgränser för antalet poddar som varje nod körs med både kubenet- och Azure CNI-nätverk. För att hantera utskalning av händelser eller klusteruppgraderingar behöver du också ytterligare IP-adresser som är tillgängliga för användning i det tilldelade undernätet. Det här ytterligare adressutrymmet är särskilt viktigt om du använder Windows Server-behållare (för närvarande i förhandsversion i AKS), eftersom nodpoolerna kräver en uppgradering för att tillämpa de senaste säkerhetskorrigeringarna. Mer information om Windows Server-noder finns [i Uppgradera en nodpool i AKS][nodepool-upgrade].

Information om hur du beräknar den IP-adress som krävs finns [i Konfigurera Azure CNI-nätverk i AKS][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet nätverk

Även om kubenet inte kräver att du konfigurerar de virtuella nätverken innan klustret distribueras, finns det nackdelar:

* Noder och poddar placeras på olika IP-undernät. Udr (User Defined Routing) och IP-vidarebefordran används för att dirigera trafik mellan poddar och noder. Den här ytterligare routningen kan minska nätverkets prestanda.
* Anslutningar till befintliga lokala nätverk eller peering till andra virtuella Azure-nätverk kan vara komplexa.

Kubenet är lämplig för små utvecklings- eller testarbetsbelastningar, eftersom du inte behöver skapa det virtuella nätverket och undernäten separat från AKS-klustret. Enkla webbplatser med låg trafik, eller för att lyfta och flytta arbetsbelastningar till behållare, kan också dra nytta av enkelheten i AKS-kluster som distribueras med kubenet-nätverk. För de flesta produktionsdistributioner bör du planera för och använda Azure CNI-nätverk. Du kan också [konfigurera dina egna IP-adressintervall och virtuella nätverk med kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Fördela inträngningstrafik

**Vägledning om bästa praxis** - Om du vill distribuera HTTP- eller HTTPS-trafik till dina program använder du inträngningsresurser och styrenheter. Ingående styrenheter tillhandahåller ytterligare funktioner över en vanlig Azure-belastningsutjämning och kan hanteras som inbyggda Kubernetes-resurser.

En Azure-belastningsutjämnare kan distribuera kundtrafik till program i AKS-klustret, men det är begränsat i vad den förstår om den trafiken. En belastningsutjämnad resurs fungerar på lager 4 och distribuerar trafik baserat på protokoll eller portar. De flesta webbprogram som använder HTTP eller HTTPS bör använda Kuberenetes ingress-resurser och styrenheter, som fungerar på lager 7. Ingress kan distribuera trafik baserat på url:en för programmet och hantera TLS/SSL-avslutning. Den här möjligheten minskar också antalet IP-adresser som du exponerar och kartlägger. Med en belastningsutjämnare behöver varje program vanligtvis en offentlig IP-adress som tilldelas och mappas till tjänsten i AKS-klustret. Med en ingående resurs kan en enda IP-adress distribuera trafik till flera program.

![Diagram som visar ingående trafikflöde i ett AKS-kluster](media/operator-best-practices-network/aks-ingress.png)

 Det finns två komponenter för inträngning:

 * En ingående *resurs*och
 * En ingående *styrenhet*

Ingressresursen är ett YAML-manifest `kind: Ingress` som definierar värden, certifikaten och reglerna för att dirigera trafik till tjänster som körs i AKS-klustret. Följande exempel YAML manifest skulle distribuera trafik för *myapp.com* till en av två tjänster, *blogservice* eller *storeservice*. Kunden dirigeras till den ena eller den andra tjänsten baserat på webbadressen de får åtkomst till.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

En ingående styrenhet är en demon som körs på en AKS-nod och klockor för inkommande begäranden. Trafiken fördelas sedan baserat på de regler som definieras i ingressresursen. Den vanligaste ingress-styrenheten är baserad på [NGINX]. AKS begränsar dig inte till en viss styrenhet, så du kan använda andra styrenheter som [Contour,][contour] [HAProxy][haproxy]eller [Traefik][traefik].

Ingående styrenheter måste schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingressstyrenheten. Använd en nodväljare i YAML-manifestet eller Helm-diagramdistributionen för att ange att resursen ska köras på en Linux-baserad nod. Mer information finns i [Använda nodväljare för att styra var poddar är schemalagda i AKS][concepts-node-selectors].

Det finns många scenarier för inträngning, inklusive följande hjälpguider:

* [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
* [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
* [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
* Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera [TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Säker trafik med en brandvägg för webbprogram (WAF)

**Vägledning för bästa praxis** – Om du vill söka igenom inkommande trafik efter potentiella attacker använder du en brandvägg för webbprogram (WAF) som [Barracuda WAF för Azure][barracuda-waf] eller Azure Application Gateway. Dessa mer avancerade nätverksresurser kan också dirigera trafik utöver bara HTTP- och HTTPS-anslutningar eller grundläggande SSL-avslutning.

En ingående styrenhet som distribuerar trafik till tjänster och program är vanligtvis en Kubernetes-resurs i AKS-klustret. Styrenheten körs som en demon på en AKS-nod och förbrukar några av nodens resurser som CPU, minne och nätverksbandbredd. I större miljöer vill du ofta avlasta en del av den här trafikroutningen eller TLS-avslutningen till en nätverksresurs utanför AKS-klustret. Du vill också söka igenom inkommande trafik efter potentiella attacker.

![En brandvägg för webbprogram (WAF) som Azure App Gateway kan skydda och distribuera trafik för AKS-klustret](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

En brandvägg för webbprogram (WAF) ger ytterligare ett säkerhetslager genom att filtrera inkommande trafik. OWASP (Open Web Application Security Project) innehåller en uppsättning regler för att titta efter attacker som skript över flera webbplatser eller cookie-förgiftning. [Azure Application Gateway][app-gateway] (för närvarande i förhandsversion i AKS) är en WAF som kan integreras med AKS-kluster för att tillhandahålla dessa säkerhetsfunktioner, innan trafiken når ditt AKS-kluster och program. Andra tredjepartslösningar utför också dessa funktioner, så att du kan fortsätta att använda befintliga investeringar eller expertis i en viss produkt.

Belastningsutjämnare eller ingående resurser fortsätter att köras i AKS-klustret för att ytterligare förfina trafikdistributionen. App Gateway kan hanteras centralt som en ingående styrenhet med en resursdefinition. För att komma [igång, skapa en Application Gateway Ingress controller][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Styra trafikflödet med nätverksprinciper

**Vägledning för bästa praxis** – Använd nätverksprinciper för att tillåta eller neka trafik till poddar. Som standard tillåts all trafik mellan poddar i ett kluster. För förbättrad säkerhet definierar du regler som begränsar pod-kommunikationen.

Nätverksprincipen är en Kubernetes-funktion som gör att du kan styra trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar som tilldelade etiketter, namnområde eller trafikport. Användningen av nätverksprinciper ger ett molnbaserat sätt att styra trafikflödet. Eftersom poddar skapas dynamiskt i ett AKS-kluster kan de nödvändiga nätverksprinciperna tillämpas automatiskt. Använd inte Azure-nätverkssäkerhetsgrupper för att styra pod-to-pod-trafik, använd nätverksprinciper.

Om du vill använda nätverksprincipen måste funktionen aktiveras när du skapar ett AKS-kluster. Du kan inte aktivera nätverksprincipen i ett befintligt AKS-kluster. Planera i förväg för att se till att du aktiverar nätverksprincipen i kluster och kan använda dem efter behov. Nätverksprincipen bör endast användas för Linux-baserade noder och poddar i AKS.

En nätverksprincip skapas som en Kubernetes-resurs med hjälp av ett YAML-manifest. Principerna tillämpas på definierade poddar och sedan definierar regler för ingående eller utgående hur trafiken kan flöda. I följande exempel används en nätverksprincip på poddar med *appen: backend-etikett* som tillämpas på dem. Ingressregeln tillåter då endast trafik från poddar med *appen: frontend-etikett:*

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Information om hur du kommer igång med principer finns i [Säker trafik mellan poddar med hjälp av nätverksprinciper i Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Anslut till noder säkert via en skyddsvärd

**Vägledning för bästa praxis** – Exponera inte fjärranslutning till AKS-noderna. Skapa en skyddsvärd eller hoppruta i ett virtuellt hanteringsnätverk. Använd skyddsvärden för att dirigera trafik till AKS-klustret på ett säkert sätt till fjärrhanteringsuppgifter.

De flesta åtgärder i AKS kan slutföras med hjälp av Azure-hanteringsverktygen eller via Kubernetes API-server. AKS-noder är inte anslutna till det offentliga internet och är endast tillgängliga i ett privat nätverk. Om du vill ansluta till noder och utföra underhålls- eller felsökningsproblem dirigerar du dina anslutningar genom en skyddsvärd eller hoppruta. Den här värden bör finnas i ett separat virtuellt hanteringsnätverk som är säkert peered till AKS-klustret virtuella nätverk.

![Ansluta till AKS-noder med hjälp av en skyddsvärd eller hoppruta](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Ledningsnätverket för skyddsvärden bör också säkras. Använd en [Azure ExpressRoute-][expressroute] eller [VPN-gateway][vpn-gateway] för att ansluta till ett lokalt nätverk och kontrollera åtkomst med hjälp av nätverkssäkerhetsgrupper.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på nätverksanslutning och säkerhet. Mer information om nätverksgrunder i Kubernetes finns i [Nätverksbegrepp för program i Azure Kubernetes Service (AKS)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool