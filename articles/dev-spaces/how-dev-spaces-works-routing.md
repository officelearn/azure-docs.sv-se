---
title: Så här fungerar routning med Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver de processer som driver Azure Dev Spaces och hur routning fungerar
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241392"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Så här fungerar routning med Azure Dev Spaces

Azure Dev Spaces ger dig flera sätt att snabbt iterera och felsöka Kubernetes-program och samarbeta med ditt team i ett AKS-kluster (Azure Kubernetes Service). När projektet körs i ett utvecklingsutrymme tillhandahåller Azure Dev Spaces ytterligare funktioner för nätverk och routning för ditt projekt.

I den här artikeln beskrivs hur routning fungerar med Dev Spaces.

## <a name="how-routing-works"></a>Så här fungerar routning

Ett dev-utrymme byggs ovanpå AKS och använder samma [nätverkskoncept](../aks/concepts-network.md). Azure Dev Spaces har också en centraliserad *ingressmanager-tjänst* och distribuerar sin egen Ingress Controller till AKS-klustret. *Ingressmanager-tjänsten* övervakar AKS-kluster med utvecklingsutrymmen och utökar Azure Dev Spaces Ingress Controller i klustret med Ingress-objekt för routning till programpoddar. Behållaren devspaces-proxy i varje `azds-route-as` pod lägger till ett HTTP-huvud för HTTP-trafik i ett utvecklingsutrymme baserat på URL:en. En begäran till URL:en *http://azureuser.s.default.serviceA.fedcba09...azds.io* får till `azds-route-as: azureuser`exempel ett HTTP-huvud med . Behållaren devspaces-proxy lägger `azds-route-as` inte till ett huvud om det redan finns något.

När en HTTP-begäran görs till en tjänst utanför klustret går begäran till ingress-styrenheten. Den ingående styrenheten dirigerar begäran direkt till lämplig pod baserat på dess ingående objekt och regler. Behållaren devspaces-proxy i podden tar `azds-route-as` emot begäran, lägger till huvudet baserat på URL:en och dirigerar sedan begäran till programbehållaren.

När en HTTP-begäran görs till en tjänst från en annan tjänst i klustret går begäran först via den anropande tjänstens devspaces-proxy-behållare. Behållaren devspaces-proxy tittar på HTTP-begäran och kontrollerar `azds-route-as` huvudet. Baserat på huvudet kommer devspaces-proxy-behållaren att slå upp IP-adressen för tjänsten som är associerad med huvudvärdet. Om en IP-adress hittas omdirigerar behållaren devspaces-proxy begäran till den IP-adressen. Om en IP-adress inte hittas dirigerar behållaren devspaces-proxy begäran till den överordnade programbehållaren.

Till exempel distribueras *programtjänstenA* och *serviceB* till ett överordnat dev-utrymme som kallas *standard*. *serviceA* förlitar sig på *serviceB* och gör HTTP-anrop till den. Azure User skapar ett underordnat utvecklingsutrymme baserat på *standardutrymmet* som kallas *azureuser*. Azure-användare distribuerar också sin egen version av *serviceA* till sitt underordnade utrymme. När en begäran *http://azureuser.s.default.serviceA.fedcba09...azds.io*görs till:

![Routning av Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Ingress-styrenheten söker upp IP-adressen för podden som är associerad med URL:en, som är *serviceA.azureuser*.
1. Ingress-styrenheten hittar IP-adressen för podden i Azure-användarens utvecklingsutrymme och dirigerar begäran till *serviceA.azureuser-podden.*
1. Devspaces-proxy-behållaren i *serviceA.azureuser-podden* `azds-route-as: azureuser` tar emot begäran och lägger till som ett HTTP-huvud.
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod dirigerar begäran till *serviceA-programbehållaren* i *serviceA.azureuser* pod.
1. *ServiceA-programmet* i *serviceA.azureuser* pod ringer till *serviceB*. *ServiceA-programmet* innehåller också kod för `azds-route-as` att bevara det `azds-route-as: azureuser`befintliga huvudet, som i det här fallet är .
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod tar emot begäran och slår upp IP-adressen *för serviceB* baserat på värdet för `azds-route-as` huvudet.
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod hittar inte en IP för *serviceB.azureuser*.
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod söker upp IP för *serviceB* i det överordnade utrymmet, som är *serviceB.default*.
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod hittar IP för *serviceB.default* och dirigerar begäran till *serviceB.default* pod.
1. Devspaces-proxy-behållaren i *serviceB.default-pod* tar emot begäran och dirigerar begäran till *serviceB-programbehållaren* i *serviceB.default-podden.*
1. *ServiceB-programmet* i *serviceB.default* pod returnerar ett svar till *serviceA.azureuser* pod.
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod tar emot svaret och dirigerar svaret till *serviceA-programbehållaren* i *serviceA.azureuser* pod.
1. *ServiceA-programmet* tar emot svaret och returnerar sedan sitt eget svar.
1. Devspaces-proxy-behållaren i *serviceA.azureuser* pod tar emot svaret från *serviceA-programbehållaren* och dirigerar svaret till den ursprungliga anroparen utanför klustret.

All annan TCP-trafik som inte är HTTP passerar genom ingress-styrenheten och devspaces-proxy-behållare oförändrade.

## <a name="sharing-a-dev-space"></a>Dela ett utvecklingsutrymme

När du arbetar med ett team kan du [dela ett utvecklingsutrymme över ett helt team](how-to/share-dev-spaces.md) och skapa härledda utvecklingsutrymmen. Ett utvecklingsutrymme kan användas av alla som har deltagaråtkomst till dev-utrymmets resursgrupp.

Du kan också skapa ett nytt utvecklingsutrymme som härleds från ett annat utvecklingsutrymme. När du skapar ett härlett utvecklingsutrymme läggs *azds.io/parent-space=PARENT-SPACE-NAME-etiketten* till i namnområdet för härledda dev-utrymme. Dessutom delas alla program från det överordnade utvecklingsutrymmet med det härledda utvecklingsutrymmet. Om du distribuerar en uppdaterad version av ett program till det härledda utvecklingsutrymmet finns den bara i det härledda utvecklingsutrymmet och det överordnade utvecklingsutrymmet påverkas inte. Du kan ha högst tre nivåer av härledda dev spaces eller *morförälder* utrymmen.

Det härledda utvecklingsutrymmet dirigerar också begäranden mellan sina egna program och de program som delas från den överordnade. Routningen fungerar genom att försöka dirigera begäran till ett program i det härledda utvecklingsutrymmet och falla tillbaka till det delade programmet från det överordnade dev-utrymmet. Routningen kommer att falla tillbaka till det delade programmet i morföräldersutrymmet om programmet inte finns i det överordnade utrymmet.

Ett exempel:
* *Standardinställningen* för dev-utrymme har program *serviceA* och *serviceB*.
* Dev space *azureuser* härleds från *standard*.
* En uppdaterad version av *serviceA* distribueras till *azureuser*.

När du använder *azureuser*dirigeras alla begäranden till *serviceA* till den uppdaterade versionen i *azureuser*. En begäran till *serviceB* försöker först dirigeras till *azureuser-versionen* av *serviceB*. Eftersom den inte finns dirigeras den till *standardversionen* av *serviceB*. Om *azureuser-versionen* av *serviceA* tas bort, kommer alla begäranden till *serviceA* att återgå till att använda *standardversionen* av *serviceA*.

## <a name="next-steps"></a>Nästa steg

Mer information om hur Azure Dev Spaces använder routning för att tillhandahålla snabb iteration och utveckling finns i [Hur det fungerar att ansluta utvecklingsdatorn till ditt utvecklingsutrymme][how-it-works-connect], Hur [fjärrfelsökning av koden med Azure Dev Spaces fungerar][how-it-works-remote-debugging]och [GitHub-åtgärder & Azure Kubernetes Service][pr-flow].

Information om hur du kommer igång med routning med Azure Dev Spaces för teamutveckling finns [i teamutvecklingen i snabbstarten för Azure Dev Spaces.][quickstart-team]

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md