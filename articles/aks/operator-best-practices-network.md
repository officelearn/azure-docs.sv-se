---
title: Operatorn bästa praxis - nätverksanslutning i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för resurser för virtuella nätverk och anslutning i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: iainfou
ms.openlocfilehash: 2bdc18ba4dc77178d5fcc5d2ba6d89aa109d923c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074145"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Metodtips för nätverksanslutning och säkerhet i Azure Kubernetes Service (AKS)

När du skapar och hantera kluster i Azure Kubernetes Service (AKS) kan du ange nätverksanslutning för dina noder och program. Dessa resurser omfattar IP-adressintervall, belastningsutjämnare och ingress-styrenheter. Om du vill upprätthålla en hög kvalitet på tjänsten för dina program, måste du planera för och konfigurera sedan dessa resurser.

Den här bästa praxis-artikeln fokuserar på nätverksanslutning och säkerhet för klusteroperatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Jämför kubenet och Azure CNI nätverk lägen i AKS
> * Planera för obligatoriska IP-adresser och anslutning
> * Distribuera trafik med belastningsutjämnare, ingående domänkontrollanter eller en brandväggar för webbprogram (WAF)
> * Anslut säkert till klusternoderna

## <a name="choose-the-appropriate-network-model"></a>Välj lämpliga nätverks-modell

**Bästa praxis riktlinjer** – för integrering med befintliga virtuella nätverk eller lokala nätverk använder CNI för Azure-nätverk i AKS. Den här modellen för nätverk kan också större uppdelning av resurser och kontroller i en företagsmiljö.

Virtuella nätverk tillhandahåller grundläggande anslutning för AKS-noder och kunder att få åtkomst till dina program. Det finns två olika sätt att distribuera AKS-kluster i virtuella nätverk:

* **Kubenet nätverk** -resurser för virtuella nätverk hanteras av Azure när klustret har distribuerats och använder den [kubenet] [ kubenet] Kubernetes-plugin-programmet.
* **Azure CNI nätverk** – distribuerar till ett befintligt virtuellt nätverk och använder den [Azure behållare nätverk gränssnitt (CNI)] [ cni-networking] Kubernetes-plugin-programmet. Poddar får enskilda IP-adresser som kan dirigera till andra nätverkstjänster eller lokala resurser.

Behållare nätverk gränssnitt (CNI) är en oberoende protokoll som gör att container runtimes göra begäranden till en nätverksleverantör av. Azure-CNI tilldelar IP-adresser till poddar och noder och tillhandahåller IP-adress adresshantering (IPAM) funktioner när du ansluter till befintliga Azure-nätverk. Varje nod och pod resurs tar emot en IP-adress i Azure-nätverk och ingen ytterligare routning krävs för att kommunicera med andra resurser och tjänster.

![Diagram över två noder med bryggor ansluta dem till ett enda Azure virtuellt nätverk](media/operator-best-practices-network/advanced-networking-diagram.png)

För de flesta distributioner av produktion bör du använda Azure CNI nätverk. Den här modellen för nätverk möjliggör uppdelning av kontroll och hanteringen av resurser. Från ett säkerhetsperspektiv vill du ofta olika team att hantera och skydda dessa resurser. Azure CNI nätverk kan du ansluta till befintliga Azure-resurser, lokala resurser eller andra tjänster direkt via IP-adresser som tilldelats varje pod.

När du använder Azure CNI nätverk är den virtuella nätverksresursen i en separat resursgrupp till AKS-klustret. Delegera behörigheter för AKS-tjänstens huvudnamn komma åt och hantera dessa resurser. Tjänstens huvudnamn som används av AKS-klustret måste ha minst [Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor) behörigheter på undernätet i det virtuella nätverket. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) istället för att använda den inbyggda rollen som Nätverksdeltagare, krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Läs mer om AKS-tjänsten huvudnamn delegering [delegera åtkomst till andra Azure-resurser][sp-delegation].

Som varje nod och pod få sin egen IP-adress kan du planera adressintervallen för AKS-undernät. Undernätet måste vara tillräckligt stor för att ange IP-adresser för varje nod, poddar och nätverksresurser som du distribuerar. Varje AKS-kluster måste placeras i ett eget undernät. Om du vill tillåta anslutning till lokala eller peer-kopplade nätverk i Azure, Använd inte IP-adressintervall som överlappar med befintliga nätverksresurser. Det finns standardbegränsningar att antalet poddar som varje nod som körs med både kubenet och CNI för Azure-nätverk. Om du vill hantera att skala upp händelser eller klusteruppgradering, måste du också ytterligare IP-adresser som är tillgängliga för användning i det tilldelade undernätet. Den här ytterligare adressutrymme är särskilt viktigt om du använder Windows Server-behållare (för närvarande i förhandsversion i AKS), eftersom dessa nodpooler kräver en uppgradering för att tillämpa de senaste säkerhetsuppdateringar. Mer information om Windows Server-noder finns i [uppgradera en nodpool i AKS][nodepool-upgrade].

Att beräkna IP-adress krävs, se [CNI konfigurera Azure-nätverk i AKS][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet nätverk

Kubenet behöver du inte konfigurera de virtuella nätverken innan klustret har distribuerats, finns men det nackdelar:

* Noder och poddar placeras på olika IP-undernät. Användardefinierad routning (UDR) och IP-vidarebefordring används för att vidarebefordra trafik mellan poddar och noder. Den här ytterligare routning kan minska nätverkets prestanda.
* Anslutningar till befintliga lokala nätverk eller peer-kopplingen till andra Azure-nätverk kan vara komplexa.

Kubenet är lämplig för små arbetsbelastningar för utveckling eller testning, eftersom du inte behöver skapa virtuellt nätverk och undernät separat från AKS-klustret. Enkel webbplatser med låg trafik, eller för att lyfta och flytta arbetsbelastningar i behållare, kan också dra nytta av enkelheten med AKS-kluster som distribueras med kubenet nätverk. För de flesta distributioner av produktion bör du planera för och använder Azure CNI nätverk. Du kan också [konfigurera dina egna IP-adressintervall och virtuella nätverk med kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuera inkommande trafik

**Bästa praxis riktlinjer** – om du vill distribuera HTTP eller HTTPS-trafik till dina program, använda ingångs-resurser och domänkontrollanter. Ingress-domänkontrollanter tillhandahåller ytterligare funktioner via vanliga Azure-belastningsutjämnare och kan hanteras som interna Kubernetes-resurser.

En Azure belastningsutjämnare kan distribuera kunden trafik till program i AKS-kluster, men är begränsad i vad den förstår om trafiken. En belastningsutjämningsresurs fungerar på lager 4 och distribuerar trafiken baserat på protokoll eller portar. De flesta webbprogram som använder HTTP eller HTTPS bör använda Kuberenetes ingående resurser och styrenheter som fungerar på lager 7. Ingående kan distribuera trafik baserat på URL: en för TLS/SSL-avslutning program och hantera. Den här möjligheten minskar också antalet IP-adresser som du exponera och mappa. Med en belastningsutjämnare behovet varje av en offentlig IP-adress tilldelas och mappas till tjänsten i AKS-klustret. En IP-adress kan distribuera trafik till flera program med en ingress-resurs.

![Diagram över ingående trafikflödet i ett AKS-kluster](media/operator-best-practices-network/aks-ingress.png)

 Det finns två komponenter för inkommande via:

 * En ingress *resource*, och
 * En ingress *controller*

Ingress-resursen är en YAML-manifestet för `kind: Ingress` som definierar värden, certifikat och regler för att dirigera trafik till tjänster som körs i AKS-klustret. Följande exempel YAML manifestet skulle distribuera trafik för *myapp.com* till en av två tjänster *blogservice* eller *storeservice*. Kunden dirigeras till en tjänst eller den andra baserat på URL: en som de har åtkomst till.

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

Ingress-kontrollant är en daemon som körs på ett AKS-noder och söker efter inkommande begäranden. Trafiken distribueras sedan baserat på de regler som definierats i ingress-resursen. Den vanligaste ingress-kontrollanten baseras på [NGINX]. AKS inte begränsa du till en specifik domänkontrollant, så du kan använda andra domänkontrollanter som [profil][contour], [HAProxy][haproxy], eller [ Traefik][traefik].

Ingress-styrenheter måste schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingress-kontrollant. Använd en nod väljare i YAML-manifest eller Helm-diagram-distribution för att ange att resursen ska köras på en Linux-baserade nod. Mer information finns i [använda väljare för noden att kontroll där poddar schemaläggs i AKS][concepts-node-selectors].

Det finns många scenarier för ingress, inklusive följande guider:

* [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
* [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
* [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
* Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Skydda trafik med en brandvägg för webbaserade program (WAF)

**Bästa praxis riktlinjer** – om du vill skanna inkommande trafik för eventuella attacker, använda en brandvägg för webbaserade program (WAF) som [Barracuda WAF for Azure] [ barracuda-waf] eller Azure Application Gateway. De här mer avancerade nätverksresurser kan också dirigera trafik utöver bara HTTP och HTTPS-anslutningar eller grundläggande SSL-avslutning.

En ingress-kontrollant som distribuerar trafiken till tjänster och program är vanligtvis en Kubernetes-resurs i AKS-klustret. Styrenheten körs som en daemon på ett AKS-noder och förbrukar några av nodens resurser, till exempel processor, minne och bandbredd i nätverket. I miljöer med större du ofta omfördela några av den här routning av nätverkstrafik eller TLS-avslutning till en nätverksresurs utanför AKS-klustret. Du bör också söker igenom inkommande trafik för eventuella attacker.

![En brandvägg för webbaserade program (WAF), till exempel Azure App Gateway kan skydda och distribuera trafik för AKS-kluster](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

En brandvägg för webbaserade program (WAF) ger ett extra säkerhetslager genom att filtrera inkommande trafik. Den öppna OWASP Web Application Security Project () innehåller en uppsättning regler för att se upp för attacker som skriptkörning över flera webbplatser eller cookie förgiftning. [Azure Application Gateway] [ app-gateway] (för närvarande i förhandsversion i AKS) är en WAF som kan integreras med AKS-kluster tillhandahåller dessa säkerhetsfunktioner innan trafiken når dina AKS-kluster och program. Andra lösningar från tredje part kan du också utföra dessa funktioner, så att du kan fortsätta att använda befintliga investeringar eller expertis i en viss produkt.

Load balancer eller ingående resurser fortsätter att köras i AKS-klustret att ytterligare förfina fördelning av trafik. App Gateway kan hanteras centralt som en ingress-kontrollant med en resursdefinition. Du kommer igång [skapa en Application Gateway Ingress-kontrollant][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Kontrollera trafikflödet med nätverksprinciper

**Bästa praxis riktlinjer** -använda nätverksprinciper för att tillåta eller neka trafik till poddar. Som standard tillåts all trafik mellan poddar i ett kluster. Definiera regler som begränsar pod kommunikation för förbättrad säkerhet.

Nätverksprincip är en Kubernetes-funktion som låter dig styra trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar, till exempel tilldelade etiketter, namnområde eller trafik port. Användning av nätverksprinciper ger ett molnbaserade sätt att styra flödet av trafik. Då poddar skapas dynamiskt i ett AKS-kluster, kan de nödvändiga nätverksprinciperna tillämpas automatiskt. Använd inte Azure nätverkssäkerhetsgrupper för att kontrollera pod-pod-trafik, använda nätverksprinciper.

Om du vill använda principen för nätverk, måste funktionen aktiveras när du skapar ett AKS-kluster. Du kan inte aktivera principen för nätverk i ett befintligt AKS-kluster. Planera framåt och se till att du aktiverar nätverksprincip i kluster och använda dem vid behov. Nätverksprincip bör endast användas för Linux-baserade noder och poddar i AKS.

En princip skapas som en Kubernetes-resurs med hjälp av en YAML-manifestet. Principerna tillämpas på definierade poddar och regler för inkommande eller utgående definiera hur trafiken kan flöda. I följande exempel gäller en princip för poddar med den *app: serverdel* etiketten tillämpas. Regel för inkommande sedan tillåter endast trafik från poddar med den *app: frontend* etikett:

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

Kom igång med principer, se [skydda trafik mellan poddar med hjälp av principer för nätverk i Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Anslut säkert till noderna genom en skyddsmiljö-värd

**Bästa praxis riktlinjer** -exponera inte fjärranslutningar till AKS-noder. Skapa en skyddsmiljö-värd eller hoppa ruta i ett virtuellt nätverk för hantering. Använd skyddsmiljö-värd för att dirigera trafik på ett säkert sätt i AKS-klustret till uppgifter för fjärrhantering.

De flesta åtgärder i AKS kan utföras med hjälp av Azure-hanteringsverktyg eller via Kubernetes API-servern. AKS-noder inte är anslutna till det offentliga internet och är bara tillgängliga i ett privat nätverk. Dirigera dina anslutningar via en skyddsmiljö-värd för att ansluta till noder och utföra underhåll eller felsöka problem, eller hoppa box. Den här värden måste vara i ett virtuellt nätverk som är separat hantering som är på ett säkert sätt peer-kopplat till det virtuella nätverket för AKS-kluster.

![Ansluta till AKS-noder med en skyddsmiljö-värd eller hoppa box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Hanteringsnätverket för skyddsmiljö-värd bör skyddas, för. Använd en [Azure ExpressRoute] [ expressroute] eller [VPN-gateway] [ vpn-gateway] kan ansluta till ett lokalt nätverk och styra åtkomst med hjälp av nätverkssäkerhet grupper.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på nätverksanslutning och säkerhet. Läs mer om grunderna i nätverket i Kubernetes [Network begrepp för program i Azure Kubernetes Service (AKS)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
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