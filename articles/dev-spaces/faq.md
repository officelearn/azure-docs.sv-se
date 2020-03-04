---
title: Vanliga frågor och svar om Azure dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Hitta svar på några vanliga frågor om Azure dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 285fdb7892d2da40dd50e025cb1dd7644ec17ae0
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255715"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Vanliga frågor och svar om Azure dev Spaces

Här är några vanliga frågor om Azure dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Vilka Azure-regioner tillhandahåller för närvarande Azure dev Spaces?

Se [regioner som stöds][supported-regions] för en fullständig lista över tillgängliga regioner.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Kan jag använda Azure dev Spaces med befintliga Dockerfiles-eller Helm-diagram?

Ja, om projektet redan har ett Dockerfile-eller Helm-diagram kan du använda dessa filer med Azure dev Spaces. När du kör `azds prep`använder du parametern `--chart` och anger placeringen för diagrammet. Azure dev Spaces kommer fortfarande att generera en *azds. yaml* -och *Dockerfile. utvecklar* -fil, men den ersätter eller ändrar inte ett befintligt Dockerfile eller ett Helm-diagram. Du kan behöva ändra *azds. yaml* och *Dockerfile. utveckla* filer för att allt ska fungera korrekt med ditt befintliga program när du kör `azds up`.

När du använder ett eget Dockerfile-eller Helm-diagram finns följande begränsningar:
* Om du bara använder en Dockerfile måste den innehålla allt du behöver för att aktivera utvecklings scenarier, till exempel språk-SDK inte bara för körning. Om du använder en separat Dockerfile för Azure dev Spaces, till exempel en Dockerfile. utveckla, måste allt du behöver för att aktivera utvecklings scenarier ingå i Dockerfile.
* Helm-diagrammet måste ha stöd för att skicka delar av eller hela bildtaggen som ett värde från *Values. yaml*.
* Om du ändrar något med ingress kan du också uppdatera Helm-diagrammet för att använda den ingångs lösning som tillhandahålls av Azure dev Spaces.
* Om du vill använda [routningsfunktioner som tillhandahålls av Azure dev Spaces][dev-spaces-routing]måste alla tjänster för ett enskilt projekt rymmas i ett enda Kubernetes-namnområde och måste distribueras med enkel namngivning, till exempel *service-a*. I standard Helm-diagram kan den här namngivnings uppdateringen göras genom att ange ett värde för egenskapen *fullnameOverride* .

Om du vill jämföra ditt eget Dockerfile-eller Helm-diagram med en befintlig version som fungerar med Azure dev Spaces, granskar du filerna som genereras i [snabb][quickstart-cli]starten.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Kan jag ändra filerna som genereras av Azure dev Spaces?

Ja, du kan ändra *azds. yaml* -filen, Dockerfile och Helm-diagrammet [som genereras av Azure dev Spaces när du förbereder projektet][dev-spaces-prep]. Om du ändrar de här filerna ändras hur projektet skapas och körs.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan jag använda Azure dev Spaces utan en offentlig IP-adress?

Nej, du kan inte etablera Azure dev Spaces i ett AKS-kluster utan en offentlig IP-adress. En offentlig IP [krävs av Azure dev Spaces för routning][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan jag använda mina egna ingress med Azure dev Spaces?

Ja, du kan konfigurera dina egna ingångs sidor på sidan ingångs steg för Azure dev. Du kan till exempel använda [traefik][ingress-traefik] eller [nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan jag använda HTTPS med Azure dev Spaces?

Ja, du kan konfigurera dina egna ingress med HTTPS med hjälp av [traefik][ingress-https-traefik] eller [nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan jag använda Azure dev Spaces i ett kluster som använder CNI i stället för Kubernetes? 

Ja, du kan använda Azure dev Spaces i ett AKS-kluster som använder CNI för nätverk. Du kan till exempel använda Azure dev Spaces i ett AKS-kluster med [befintliga Windows-behållare][windows-containers]som använder cni för nätverk. Mer information om hur du använder CNI för nätverk med Azure dev Spaces finns [här](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan jag använda Azure dev Spaces med Windows-behållare?

För närvarande är Azure dev Spaces endast avsett att köras på Linux-poddar och noder, men du kan köra Azure dev Spaces i ett AKS-kluster med [befintliga Windows-behållare][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan jag använda Azure dev Spaces på AKS-kluster med autentiserade IP-adressintervall för API-servern?

Ja, du kan använda Azure dev Spaces på AKS-kluster med [autentiserade IP-adressintervall för API-servern][aks-auth-range] aktiverat. Mer information om hur du använder ett AKS-kluster med auktoriserade IP-adressintervall med API-server som är aktiverade med Azure dev Spaces finns [här](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kan jag använda Azure dev Spaces på AKS-kluster med begränsad utgående trafik för klusternoder?

Ja, du kan använda Azure dev Spaces på AKS-kluster med [begränsad utgående trafik för klusternoder][aks-restrict-egress-traffic] aktiverade när rätt fullständiga domän namn har tillåtits. Mer information om hur du använder ett AKS-kluster med begränsad utgående trafik för klusternoder som är aktiverade med Azure dev Spaces finns [här](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Kan jag använda Azure dev Spaces på RBAC-aktiverade AKS-kluster?

Ja, du kan använda Azure dev Spaces på AKS-kluster med eller utan RBAC-aktiverat.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Vad händer när jag aktiverar ingress för projekt i Visual Studio?

När du använder Visual Studio för att förbereda ditt projekt har du möjlighet att aktivera ingångar för din tjänst. Genom att aktivera ingångar skapas en offentlig slut punkt för att komma åt tjänsten när den körs på ditt AKS-kluster, vilket är valfritt. Om du inte aktiverar ingress är din tjänst bara tillgänglig i AKS-klustret.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md