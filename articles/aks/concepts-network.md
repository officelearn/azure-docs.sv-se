---
title: Begrepp - Nätverk i Azure Kubernetes Services (AKS)
description: Lär dig mer om nätverk i Azure Kubernetes Service (AKS), inklusive kubenet- och Azure CNI-nätverk, ingående styrenheter, belastningsutjämnare och statiska IP-adresser.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253941"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Nätverksbegrepp för program i Azure Kubernetes Service (AKS)

I en behållarbaserad mikrotjänstmetod för programutveckling måste programkomponenter arbeta tillsammans för att bearbeta sina uppgifter. Kubernetes tillhandahåller olika resurser som aktiverar den här programkommunikationen. Du kan ansluta till och exponera program internt eller externt. Om du vill skapa program med högtillgång kan du läsa in dina program. Mer komplexa program kan kräva konfiguration av inkommande trafik för SSL/TLS-avslutning eller routning av flera komponenter. Av säkerhetsskäl kan du också behöva begränsa flödet av nätverkstrafik till eller mellan poddar och noder.

I den här artikeln introduceras de grundläggande begrepp som ger nätverk till dina program i AKS:

- [Tjänster](#services)
- [Virtuella Azure-nätverk](#azure-virtual-networks)
- [Ingående styrenheter](#ingress-controllers)
- [Nätverksprinciper](#network-policies)

## <a name="kubernetes-basics"></a>Grundläggande om Kubernetes

Kubernetes ger ett abstraktionslager till virtuella nätverk för att ge åtkomst till dina program eller för programkomponenter för att kommunicera med varandra. Kubernetes-noder är anslutna till ett virtuellt nätverk och kan tillhandahålla inkommande och utgående anslutning för poddar. *Kube-proxy-komponenten* körs på varje nod för att tillhandahålla dessa nätverksfunktioner.

I Kubernetes grupperar *tjänsterna* logiskt poddar för att möjliggöra direkt åtkomst via en IP-adress eller DNS-namn och på en viss port. Du kan också distribuera trafik med hjälp av en *belastningsutjämnare*. Mer komplex routing av applikationstrafik kan också uppnås med *Ingress Controllers*. Säkerhet och filtrering av nätverkstrafiken för poddar är möjlig med Kubernetes *nätverksprinciper*.

Azure-plattformen hjälper också till att förenkla virtuella nätverk för AKS-kluster. När du skapar en Kubernetes belastningsutjämnare skapas och konfigureras den underliggande Azure-belastningsutjämningsresursen. När du öppnar nätverksportar till poddar konfigureras motsvarande azure-nätverkssäkerhetsgruppsregler. För HTTP-programroutning kan Azure också konfigurera *extern DNS* när nya ingående vägar har konfigurerats.

## <a name="services"></a>Tjänster

För att förenkla nätverkskonfigurationen för programarbetsbelastningar använder Kubernetes *Tjänster* för att logiskt gruppera en uppsättning poddar tillsammans och tillhandahålla nätverksanslutning. Följande tjänsttyper är tillgängliga:

- **Kluster-IP** - Skapar en intern IP-adress för användning i AKS-klustret. Bra för interna program som stöder andra arbetsbelastningar i klustret.

    ![Diagram som visar kluster-IP-trafikflödet i ett AKS-kluster][aks-clusterip]

- **NodePort** - Skapar en portmappning på den underliggande noden som gör att programmet kan nås direkt med nod-IP-adressen och porten.

    ![Diagram som visar NodePort trafikflöde i ett AKS-kluster][aks-nodeport]

- **LoadBalancer** - Skapar en Azure-belastningsutjämnadsresurs, konfigurerar en extern IP-adress och ansluter de begärda poddar till lastutjämningshanterarens serverd-pool. För att kundernas trafik ska kunna nå programmet skapas belastningsutjämningsregler på önskade portar. 

    ![Diagram som visar belastningsutjämnartrafikflödet i ett AKS-kluster][aks-loadbalancer]

    För ytterligare kontroll och routning av inkommande trafik kan du i stället använda en [ingresskontrollant](#ingress-controllers).

- **ExternalName** - Skapar en specifik DNS-post för enklare programåtkomst.

IP-adressen för belastningsutjämnare och tjänster kan tilldelas dynamiskt eller så kan du ange en befintlig statisk IP-adress som ska användas. Både interna och externa statiska IP-adresser kan tilldelas. Den här befintliga statiska IP-adressen är ofta knuten till en DNS-post.

Både *interna* och *externa* belastningsutjämnare kan skapas. Interna belastningsutjämnare tilldelas bara en privat IP-adress, så att de inte kan nås från Internet.

## <a name="azure-virtual-networks"></a>Virtuella Azure-nätverk

I AKS kan du distribuera ett kluster som använder någon av följande två nätverksmodeller:

- *Kubenet-nätverk* – Nätverksresurserna skapas vanligtvis och konfigureras när AKS-klustret distribueras.
- *CNI-nätverk (Azure Container Networking Interface)* – AKS-klustret är anslutet till befintliga virtuella nätverksresurser och konfigurationer.

### <a name="kubenet-basic-networking"></a>Kubenet (grundläggande) nätverk

Alternativet *kubenet-nätverk* är standardkonfigurationen för skapande av AKS-kluster. Med *kubenet*får noder en IP-adress från Azures virtuella nätverksundernät. Poddar får en IP-adress från ett annat logiskt adressutrymme än det virtuella Azure-nätverkets undernät för noderna. NAT (Network Address Translation) konfigureras sedan så att poddarna kan komma åt resurser i det virtuella Azure-nätverket. Källans IP-adress för trafiken är NAT'd till nodens primära IP-adress.

Noder använder [kubenet][kubenet] Kubernetes plugin. Du kan låta Azure-plattformen skapa och konfigurera de virtuella nätverken åt dig, eller välja att distribuera AKS-klustret till ett befintligt virtuellt nätverksundernät. Återigen får endast noderna en dirigerbar IP-adress och poddar använder NAT för att kommunicera med andra resurser utanför AKS-klustret. Den här metoden minskar avsevärt antalet IP-adresser som du behöver reservera i nätverksutrymmet för poddar att använda.

Mer information finns i [Konfigurera kubenet-nätverk för ett AKS-kluster][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI -nätverk (avancerat)

Med Azure CNI får varje podd en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unika i hela nätverksutrymmet och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som den stöder. Motsvarande antal IP-adresser per nod reserveras sedan på framsidan för den noden. Den här metoden kräver mer planering, eftersom det annars kan leda till IP-adressutmattning eller behovet av att återskapa kluster i ett större undernät när programmets krav växer.

Noder använder plugin:n [För Azure Container Networking Interface (CNI)][cni-networking] Kubernetes.

![Diagram som visar två noder med bryggor som ansluter var och en till ett enda Azure VNet][advanced-networking-diagram]

Mer information finns i [Konfigurera Azure CNI för ett AKS-kluster][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Jämför nätverksmodeller

Både kubenet och Azure CNI tillhandahåller nätverksanslutning för DINA AKS-kluster. Det finns dock fördelar och nackdelar med varje. På en hög nivå gäller följande överväganden:

* **kubenet (kubenet)**
    * Sparar IP-adressutrymme.
    * Använder Kubernetes interna eller externa belastningsutjämnare för att nå poddar från utanför klustret.
    * Du måste manuellt hantera och underhålla användardefinierade vägar .UDRs.You must manually manage and maintain user-defined routes (UDRs).
    * Högst 400 noder per kluster.
* **Azure CNI**
    * Poddar får full virtuell nätverksanslutning och kan nås direkt via sin privata IP-adress från anslutna nätverk.
    * Kräver mer IP-adressutrymme.

Följande beteendeskillnader finns mellan kubenet och Azure CNI:

| Funktion                                                                                   | Kubenet (på)   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Distribuera kluster i befintligt eller nytt virtuellt nätverk                                            | Stöds - UDRs manuellt tillämpas | Stöds |
| Pod-pod-anslutning                                                                         | Stöds | Stöds |
| Pod-VM-anslutning; Virtuell dator i samma virtuella nätverk                                          | Fungerar när den initieras av pod | Fungerar åt båda hållen |
| Pod-VM-anslutning; Virtuell dator i peered virtuellt nätverk                                            | Fungerar när den initieras av pod | Fungerar åt båda hållen |
| Lokal åtkomst med VPN eller Express Route                                                | Fungerar när den initieras av pod | Fungerar åt båda hållen |
| Åtkomst till resurser som skyddas av tjänstslutpunkter                                             | Stöds | Stöds |
| Exponera Kubernetes-tjänster med hjälp av en belastningsutjämnad tjänst, App Gateway eller ingress-styrenhet | Stöds | Stöds |
| Standard-Azure DNS och privata zoner                                                          | Stöds | Stöds |

När det gäller DNS, med både kubenet och Azure CNI plugins DNS erbjuds av CoreDNS, en demon som körs i AKS. Mer information om CoreDNS på Kubernetes finns [i Anpassa DNS-tjänsten](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS är konfigurerad som standard för att vidarebefordra okända domäner till nod DNS-servrar, med andra ord till DNS-funktionen i Azure Virtual Network där AKS-klustret distribueras. Därför fungerar Azure DNS och privata zoner för poddar som körs i AKS.

### <a name="support-scope-between-network-models"></a>Stöd omfattning mellan nätverksmodeller

Oavsett vilken nätverksmodell du använder kan både kubenet och Azure CNI distribueras på något av följande sätt:

* Azure-plattformen kan automatiskt skapa och konfigurera de virtuella nätverksresurserna när du skapar ett AKS-kluster.
* Du kan manuellt skapa och konfigurera de virtuella nätverksresurserna och koppla till dessa resurser när du skapar AKS-klustret.

Även om funktioner som tjänstslutpunkter eller UDRs stöds med både kubenet och Azure CNI, definierar [supportprinciperna för AKS][support-policies] vilka ändringar du kan göra. Ett exempel:

* Om du skapar de virtuella nätverksresurserna manuellt för ett AKS-kluster får du stöd när du konfigurerar egna UDR-enheter eller tjänstslutpunkter.
* Om Azure-plattformen automatiskt skapar de virtuella nätverksresurserna för AKS-klustret, stöds det inte att manuellt ändra dessa AKS-hanterade resurser för att konfigurera dina egna UDR-enheter eller tjänstslutpunkter.

## <a name="ingress-controllers"></a>Inkommande styrenheter

När du skapar en LoadBalancer-typtjänst skapas en underliggande Azure load balancer-resurs. Belastningsutjämnaren är konfigurerad för att distribuera trafik till poddar i tjänsten på en viss port. LoadBalancer fungerar bara på lager 4 - Tjänsten är omedveten om de faktiska programmen och kan inte göra några ytterligare routningsöverväganden.

*Ingress-styrenheter* arbetar på lager 7 och kan använda intelligentare regler för att distribuera programtrafik. En vanlig användning av en ingress-styrenhet är att dirigera HTTP-trafik till olika program baserat på den inkommande URL:en.

![Diagram som visar ingående trafikflöde i ett AKS-kluster][aks-ingress]

I AKS kan du skapa en ingressresurs med något som liknar NGINX eller använda AKS HTTP-programroutningsfunktionen. När du aktiverar HTTP-programroutning för ett AKS-kluster skapar *Azure-plattformen* ingressstyrenheten och en extern DNS-styrenhet. När nya ingående resurser skapas i Kubernetes skapas de DNS A-poster som krävs i en klusterspecifik DNS-zon. Mer information finns i [distribuera HTTP-programroutning][aks-http-routing].

Ett annat vanligt inslag i Ingress är SSL/TLS-avslutning. På stora webbprogram som nås via HTTPS kan TLS-avslutningen hanteras av ingressresursen i stället för inom själva programmet. Om du vill tillhandahålla automatisk TLS-certifieringsgenerering och konfiguration kan du konfigurera Ingress-resursen så att leverantörer som Let's Encrypt används. Mer information om hur du konfigurerar en NGINX-ingress-styrenhet med Let's Encrypt finns [i Ingress och TLS][aks-ingress-tls].

Du kan också konfigurera ingressstyrenheten för att bevara klientkällans IP-adress på begäranden till behållare i AKS-klustret. När en klients begäran dirigeras till en behållare i AKS-klustret via ingressstyrenheten är den ursprungliga käll-IP-adressen för den begäran inte tillgänglig för målbehållaren. När du aktiverar *IP-bevarande för klientkällan*är klientens käll-IP tillgängligt i begäranden under *X-Vidarebefordrad för*. Om du använder IP-bevarande för klientkälla på ingressstyrenheten kan du inte använda SSL-vidaregång. IP-bevarande av klientkälla och SSL-vidareförvaring kan användas med andra tjänster, till exempel *typen LoadBalancer.*

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En nätverkssäkerhetsgrupp filtrerar trafik för virtuella datorer, till exempel AKS-noder. När du skapar tjänster, till exempel en LoadBalancer, konfigurerar Azure-plattformen automatiskt alla regler för nätverkssäkerhetsgrupp som behövs. Konfigurera inte regler för nätverkssäkerhetsgrupp manuellt för att filtrera trafik för poddar i ett AKS-kluster. Definiera alla nödvändiga portar och vidarebefordran som en del av dina Kubernetes-tjänstmanifest och låt Azure-plattformen skapa eller uppdatera lämpliga regler. Du kan också använda nätverksprinciper, som beskrivs i nästa avsnitt, för att automatiskt tillämpa trafikfilterregler på poddar.

## <a name="network-policies"></a>Nätverksprinciper

Som standard kan alla poddar i ett AKS-kluster skicka och ta emot trafik utan begränsningar. För förbättrad säkerhet kanske du vill definiera regler som styr trafikflödet. Backend-program är ofta bara exponerade för nödvändiga klientdelstjänster, eller databaskomponenter är endast tillgängliga för de programnivåer som ansluter till dem.

Nätverksprincipen är en Kubernetes-funktion som är tillgänglig i AKS och som gör att du kan styra trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar som tilldelade etiketter, namnområde eller trafikport. Nätverkssäkerhetsgrupper är mer för AKS-noderna, inte poddar. Användningen av nätverksprinciper är ett mer lämpligt, molnbaserat sätt att styra trafikflödet. Eftersom poddar skapas dynamiskt i ett AKS-kluster kan de nödvändiga nätverksprinciperna tillämpas automatiskt.

Mer information finns i [Säker trafik mellan poddar med hjälp av nätverksprinciper i Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Nästa steg

För att komma igång med AKS-nätverk skapar och konfigurerar och konfigurerar du ett AKS-kluster med dina egna IP-adressintervall med [kubenet][aks-configure-kubenet-networking] eller [Azure CNI][aks-configure-advanced-networking].

För tillhörande metodtips finns i [Metodtips för nätverksanslutning och säkerhet i AKS][operator-best-practices-network].

Mer information om kubernetes och AKS-huvudbegrepp finns i följande artiklar:

- [Kubernetes / AKS kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS tillgång och identitet][aks-concepts-identity]
- [Kubernetes / AKS säkerhet][aks-concepts-security]
- [Kubernetes / AKS lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
