---
title: Begrepp - nätverk i Azure Kubernetes-tjänster (AKS)
description: Läs mer om nätverk i Azure Kubernetes Service (AKS), inklusive kubenet och Azure CNI nätverk, ingående domänkontrollanter, belastningsutjämnare och statiska IP-adresser.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 624e49cb0b211139a70d0262fbb8500deef8bf5b
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744866"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Nätverkskoncept för program i Azure Kubernetes Service (AKS)

I en behållarbaserad mikrotjänster metod för utveckling av måste programkomponenter samarbeta för att bearbeta sina uppgifter. Kubernetes tillhandahåller olika resurser som gör det här programmets kommunikation. Du kan ansluta till och exponera program internt eller externt. Om du vill skapa program med hög tillgänglighet kan du läsa in balansera dina program. Mer komplexa program kan kräva konfiguration av inkommande trafik för SSL/TLS-avslutning eller Routning av flera komponenter. Av säkerhetsskäl kan du också behöva begränsa flödet av nätverkstrafik till eller mellan poddar och noder.

Den här artikeln innehåller grundläggande begrepp som tillhandahåller nätverk till dina program i AKS:

- [Tjänster](#services)
- [Azure-nätverk](#azure-virtual-networks)
- [Ingress-styrenheter](#ingress-controllers)
- [Nätverksprinciper](#network-policies)

## <a name="kubernetes-basics"></a>Grundläggande om Kubernetes

För att tillåta åtkomst till dina program eller om programkomponenter som ska kommunicera med varandra, tillhandahåller Kubernetes ett Abstraktionslager för virtuella nätverk. Kubernetes-noderna är anslutna till ett virtuellt nätverk och kan tillhandahålla inkommande och utgående anslutningar till poddar. Den *kube-proxy* komponenten körs på alla noder att tillhandahålla dessa nätverks-funktioner.

I Kubernetes, *Services* gruppera poddarna för direkt åtkomst via en IP-adress eller DNS-namn och på en viss port. Du kan även distribuera trafik med hjälp av en *belastningsutjämnare*. Mer komplex routning av programtrafik kan även uppnås med *Ingress-styrenheter*. Säkerhet och filtrering av nätverkstrafik för poddar som är möjligt med Kubernetes *nätverksprinciper*.

Azure-plattformen hjälper också till att förenkla virtuella nätverk för AKS-kluster. När du skapar en belastningsutjämnare för Kubernetes är underliggande Azure belastningsutjämningsresursen skapas och konfigureras. När du öppnar nätverksportar till poddar konfigureras de motsvarande Azure reglerna för nätverkssäkerhetsgrupper. För HTTP-routning för program, Azure kan också konfigurera *externa DNS* som ny inkommande vägar har konfigurerats.

## <a name="services"></a>Tjänster

För att förenkla nätverkskonfiguration för programarbetsbelastningar Kubernetes använder *Services* logiskt kan gruppera en uppsättning poddar och ange nätverksanslutning. Följande typer av tjänsten är tillgängliga:

- **Kluster-IP** -skapar en intern IP-adress för användning i AKS-klustret. Bra för interna program som har stöd för andra arbetsbelastningar i klustret.

    ![Diagram över klustrets IP-trafikflödet i ett AKS-kluster][aks-clusterip]

- **NodePort** -skapar en portmappning på den underliggande nod som gör att program som kan nås direkt med noden IP-adress och port.

    ![Diagram över NodePort trafikflödet i ett AKS-kluster][aks-nodeport]

- **LoadBalancer** – skapar en Azure load balancer-resurs, konfigurerar en extern IP-adress och ansluter de begärda poddarna till belastningsutjämnarens serverdelspool. Om du vill tillåta för kunder att skapas räckvidd programmet, regler för belastningsutjämning på önskad portar. 

    ![Diagram över belastningsutjämnare trafikflödet i ett AKS-kluster][aks-loadbalancer]

    För ytterligare kontroll och routning av inkommande trafik, kan du i stället använda en [Ingress-kontrollant](#ingress-controllers).

- **ExternalName** -skapar en specifik DNS-post för enklare programåtkomst.

IP-adressen för belastningsutjämnare och tjänster kan tilldelas dynamiskt eller ange en befintlig statisk IP-adress som du använder. Både interna och externa statiska IP-adresser kan tilldelas. Den här befintlig statisk IP-adress är ofta kopplat till en DNS-post.

Båda *interna* och *externa* belastningsutjämnare kan skapas. Interna belastningsutjämnare endast har tilldelats en privat IP-adress, så kan inte nås från Internet.

## <a name="azure-virtual-networks"></a>Azures virtuella nätverk

I AKS, kan du distribuera ett kluster som använder någon av följande två modeller:

- *Kubenet* nätverket - nätverket resurser skapas och konfigureras enligt AKS-klustret distribueras normalt.
- *Azure Container nätverk gränssnitt (CNI)* nätverk – The AKS-kluster är ansluten till befintliga virtuella nätverksresurser och konfigurationer.

### <a name="kubenet-basic-networking"></a>Kubenet (grundläggande) nätverk

Den *kubenet* nätverk alternativet är standardkonfigurationen för att skapa för AKS-kluster. Med *kubenet*, noder hämta en IP-adress från det virtuella Azure-undernätet. Poddar ta emot en IP-adress från en logiskt olika adressutrymmen till undernätet för Azure-nätverk av noderna. Network adress translation (NAT) konfigureras sedan så att poddarna kan nå resurser på Azure-nätverket. Källans IP-adress av trafiken är skulle NAT till nodens primära IP-adress.

Noder använder den [kubenet] [ kubenet] Kubernetes-plugin-programmet. Du kan låta Azure-plattformen skapa och konfigurera de virtuella nätverken för dig eller välja att distribuera din AKS-kluster till ett befintligt undernät för virtuellt nätverk. Igen, använder endast de noder som tar emot en dirigerbara IP-adress och poddarna NAT för att kommunicera med andra resurser utanför AKS-klustret. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverk kan poddar att använda.

Mer information finns i [Konfigurera nätverk för ett AKS-kluster kubenet][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (Avancerat)-nätverk

Med Azure CNI varje pod får en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unikt för ditt adressutrymme för nätverket och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som stöds. Det motsvarande antalet IP-adresser per nod reserveras sedan direkt för noden. Den här metoden kräver mer planering och leder ofta till IP-adress överbelastning eller att behöva återskapa kluster i ett större undernät allteftersom dina behov växer.

Noder använder den [Azure behållare nätverk gränssnitt (CNI)] [ cni-networking] Kubernetes-plugin-programmet.

![Diagram över två noder med bryggor ansluta dem till ett enda Azure virtuellt nätverk][advanced-networking-diagram]

Azure CNI tillhandahåller följande funktioner över kubenet nätverk:

- Varje pod i klustret tilldelas en IP-adress i det virtuella nätverket. Poddarna kan kommunicera direkt med andra poddar i klustret och andra noder i det virtuella nätverket.
- Poddar i ett undernät som har aktiverat Tjänsteslutpunkter kan på ett säkert sätt ansluta till Azure-tjänster, till exempel Azure Storage och SQL DB.
- Du kan skapa användardefinierade vägar (UDR) för att dirigera trafik från poddar till en virtuell nätverksenhet.

Mer information finns i [konfigurera Azure CNI för ett AKS-kluster][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Ingress-styrenheter

När du skapar en LoadBalancer typ Service skapas en underliggande Azure belastningsutjämningsresursen. Belastningsutjämnaren har konfigurerats för att distribuera trafik till poddarna i din tjänst på en viss port. LoadBalancer-fungerar bara på layer 4 - tjänsten är inte medveten om faktiska program och kan inte göra några ytterligare överväganden för routning.

*Ingress-styrenheter* fungerar på lager 7 och smartare regler kan använda för att distribuera trafik. Ett vanligt användningsområde för Ingress-kontrollant är att dirigera HTTP-trafik till olika program baserat på inkommande URL.

![Diagram över ingående trafikflödet i ett AKS-kluster][aks-ingress]

I AKS, kan du skapa en Ingress-resurs med något som NGINX eller använda funktionen AKS HTTP programmet routning. När du aktiverar HTTP programmet routning för ett AKS-kluster kan Azure-plattformen skapar Ingress-kontrollanten och en *externt DNS* controller. Då nya Ingress-resurser skapas i Kubernetes, skapas de nödvändiga DNS A-posterna i en klusterspecifika DNS-zon. Mer information finns i [distribuera routning av HTTP-programmet][aks-http-routing].

En annan vanlig funktion för ingång är SSL/TLS-avslutning. I stora webbprogram som kan nås via HTTPS, kan TLS-avslutning hanteras av Ingress-resursen inte själva programmet. Du kan konfigurera Ingress-resurs för att använda leverantörer som krypterar vi för att tillhandahålla automatisk generering av TLS-certifiering och konfiguration. Läs mer om hur du konfigurerar en NGINX-Ingress-kontrollant med ska vi kryptera [Ingångshändelser och TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En säkerhet grupp filter nätverkstrafik för virtuella datorer, till exempel AKS-noder. När du skapar tjänster, till exempel en LoadBalancer konfigurerar Azure-plattformen automatiskt alla regler för nätverkssäkerhetsgrupper som krävs. Inte manuellt konfigurera regler för nätverkssäkerhetsgrupper för att filtrera trafik för poddar i ett AKS-kluster. Definiera alla nödvändiga portar och vidarebefordran som en del av Kubernetes Service-manifest och låt Azure-plattformen skapa eller uppdatera lämpliga regler.

Nätverkssäkerhetsgrupp finns regler för trafik som SSH som standard. Det är dessa standardregler för klusterhantering och felsöka åtkomst. Ta bort dessa standardregler kan orsaka problem med hantering av AKS och delar upp servicenivåmål (SLO).

## <a name="next-steps"></a>Nästa steg

Att komma igång med AKS nätverk, skapa och konfigurera ett AKS-kluster med dina egna IP-adressintervall med hjälp av [kubenet] [ aks-configure-kubenet-networking] eller [Azure CNI] [ aks-configure-advanced-networking].

Mer information om core Kubernetes och AKS-begrepp finns i följande artiklar:

- [Kubernetes / AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS åtkomst och identitet][aks-concepts-identity]
- [Kubernetes / AKS-säkerhet][aks-concepts-security]
- [Kubernetes / AKS-lagring][aks-concepts-storage]
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