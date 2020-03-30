---
title: Vanliga frågor och svar om Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Hitta svar på några av de vanligaste frågorna om Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s '
ms.openlocfilehash: e7b4620faa01aa9f6d46c34bafb1c623c338beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240501"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Vanliga frågor och svar om Azure Dev Spaces

Detta tar upp vanliga frågor om Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Vilka Azure-regioner tillhandahåller för närvarande Azure Dev Spaces?

Se [regioner som stöds][supported-regions] för en fullständig lista över tillgängliga regioner.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Kan jag migrera mitt AKS-kluster med Azure Dev Spaces till en annan region?

Ja, om du vill flytta aks-klustret med Azure Dev Spaces till en annan [region som stöds][supported-regions]rekommenderar vi att du skapar ett nytt kluster i den andra regionen och sedan installerar och konfigurerar Azure Dev Spaces och distribuerar dina resurser och program till ditt nya kluster. Mer information om hur du migrerar AKS finns i [Migrera till Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Kan jag använda Azure Dev Spaces med befintliga Dockerfiles eller Helm-diagram?

Ja, om projektet redan har en Dockerfile eller ett Helm-diagram kan du använda dessa filer med Azure Dev Spaces. När du `azds prep`kör `--chart` använder du parametern och anger platsen för diagrammet. Azure Dev Spaces genererar fortfarande en *filen azds.yaml* och *Dockerfile.develop,* men den ersätter eller ändrar inte en befintlig Dockerfile eller ett Helm-diagram. Du kan behöva ändra *filerna azds.yaml* och *Dockerfile.develop* för att allt ska `azds up`fungera korrekt med ditt befintliga program när du kör .

När du använder ditt eget Dockerfile- eller Helm-diagram finns följande begränsningar:
* Om du bara använder en Dockerfile måste den innehålla allt du behöver för att aktivera utvecklingsscenarier, till exempel språket SDK inte bara körningen. Om du använder en separat Dockerfile för Azure Dev Spaces, till exempel en Dockerfile.develop, måste allt du behöver för att aktivera utvecklingsscenarier inkluderas i den Dockerfile.
* Helm-diagrammet måste ha stöd för att skicka en del av eller hela bildtaggen som ett värde från *values.yaml*.
* Om du ändrar något med ingående kan du också uppdatera Helm-diagrammet för att använda den inkommande lösningen som tillhandahålls av Azure Dev Spaces.
* Om du vill använda [routningsfunktionerna i Azure Dev Spaces][dev-spaces-routing]måste alla tjänster för ett enskilt projekt passa in i ett enda Kubernetes-namnområde och måste distribueras med enkel namngivning, till exempel *service-a*. I standarddiagram för Helm kan den här namngivningsuppdateringen göras genom att ange ett värde för egenskapen *fullnameOverride.*

Om du vill jämföra ditt eget Dockerfile- eller Helm-diagram med en befintlig version som fungerar med Azure Dev Spaces granskar du de filer som genereras i [snabbstarten][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Kan jag ändra de filer som genereras av Azure Dev Spaces?

Ja, du kan ändra *azds.yaml-filen,* Dockerfile och Helm-diagrammet [som genereras av Azure Dev Spaces när du förbereder projektet][dev-spaces-prep]. Om du ändrar dessa filer ändras hur projektet byggs och körs.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan jag använda Azure Dev Spaces utan en offentlig IP-adress?

Nej, du kan inte etablera Azure Dev Spaces på ett AKS-kluster utan en offentlig IP. En offentlig IP [behövs av Azure Dev Spaces för routning][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan jag använda min egen inträngning med Azure Dev Spaces?

Ja, du kan konfigurera din egen inträngning längs sidan den ingång Azure Dev Spaces skapar. Du kan till exempel använda [traefik][ingress-traefik] eller [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan jag använda HTTPS med Azure Dev Spaces?

Ja, du kan konfigurera din egen inträngning med HTTPS med [traefik][ingress-https-traefik] eller [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan jag använda Azure Dev Spaces i ett kluster som använder CNI i stället för kubenet? 

Ja, du kan använda Azure Dev Spaces i ett AKS-kluster som använder CNI för nätverk. Du kan till exempel använda Azure Dev Spaces i ett AKS-kluster med [befintliga Windows-behållare][windows-containers], som använder CNI för nätverk. Mer information om hur du använder CNI för nätverk med Azure Dev Spaces finns [här](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan jag använda Azure Dev Spaces med Windows-behållare?

För närvarande är Azure Dev Spaces endast avsett att köras på Linux-poddar och noder, men du kan köra Azure Dev Spaces i ett AKS-kluster med [befintliga Windows-behållare][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan jag använda Azure Dev Spaces i AKS-kluster med API-serverauktoriserat IP-adressintervall aktiverat?

Ja, du kan använda Azure Dev Spaces på AKS-kluster med [API-serverauktoriserat IP-adressintervall][aks-auth-range] aktiverat. Mer information om hur du använder ett AKS-kluster med API-serverauktoriserade IP-adressintervall som är aktiverade med Azure Dev Spaces finns [här](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kan jag använda Azure Dev Spaces i AKS-kluster med begränsad utgående trafik för klusternoder?

Ja, du kan använda Azure Dev Spaces på AKS-kluster med [begränsad utgående trafik för klusternoder][aks-restrict-egress-traffic] aktiverade när rätt FQDN har tillåtits. Mer information om hur du använder ett AKS-kluster med begränsad utgående trafik för klusternoder som är aktiverade med Azure Dev Spaces finns [här](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Kan jag använda Azure Dev Spaces på RBAC-aktiverade AKS-kluster?

Ja, du kan använda Azure Dev Spaces på AKS-kluster med eller utan RBAC aktiverat.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Vad händer när jag aktiverar inträngning för projekt i Visual Studio?

När du använder Visual Studio för att förbereda projektet kan du aktivera inträngning för din tjänst. Om du aktiverar ingress skapas en offentlig slutpunkt för att komma åt din tjänst när du kör på AKS-klustret, vilket är valfritt. Om du inte aktiverar inträngning är tjänsten endast tillgänglig inifrån AKS-klustret.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Kan jag använda pod-hanterade identiteter med Azure Dev Spaces?

Azure Dev Spaces stöder för närvarande inte att använda [pod-hanterade identiteter][aks-pod-managed-id] på AKS-kluster med Azure Dev Spaces aktiverat. Om du har pod hanterade identiteter installerade och vill avinstallera det, kan du hitta mer information i [avinstallera anteckningar][aks-pod-managed-id-uninstall].

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md