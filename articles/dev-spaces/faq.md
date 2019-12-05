---
title: Vanliga frågor och svar om Azure dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Hitta svar på några vanliga frågor om Azure dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 2baab0812061bec7dcf08d35056804313d873889
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482298"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Vanliga frågor och svar om Azure dev Spaces

Här är några vanliga frågor om Azure dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Vilka Azure-regioner tillhandahåller för närvarande Azure dev Spaces?

En fullständig lista över tillgängliga regioner finns i [regioner och konfigurationer som stöds][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan jag använda Azure dev Spaces utan en offentlig IP-adress?

Nej, du kan inte etablera Azure dev Spaces i ett AKS-kluster utan en offentlig IP-adress. En offentlig IP [krävs av Azure dev Spaces för routning][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan jag använda mina egna ingress med Azure dev Spaces?

Ja, du kan konfigurera dina egna ingångs steg på sidan som skapas av Azure dev. Du kan till exempel använda [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan jag använda HTTPS med Azure dev Spaces?

Ja, du kan konfigurera dina egna ingress med HTTPS med [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan jag använda Azure dev Spaces i ett kluster som använder CNI i stället för Kubernetes? 

Ja, du kan använda Azure dev Spaces i ett AKS-kluster som använder CNI för nätverk. Du kan till exempel använda Azure dev Spaces i ett AKS-kluster med [befintliga Windows-behållare][windows-containers]som använder cni för nätverk.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan jag använda Azure dev Spaces med Windows-behållare?

För närvarande är Azure dev Spaces endast avsett att köras på Linux-poddar och noder, men du kan köra Azure dev Spaces i ett AKS-kluster med [befintliga Windows-behållare][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan jag använda Azure dev Spaces på AKS-kluster med autentiserade IP-adressintervall för API-servern?

Ja, du kan använda Azure dev Spaces på AKS-kluster med [autentiserade IP-adressintervall för API-servern][aks-auth-range] aktiverat. När du [skapar][aks-auth-range-create] klustret måste du [tillåta ytterligare intervall baserat på din region][aks-auth-range-ranges]. Du kan också [Uppdatera][aks-auth-range-update] ett befintligt kluster för att tillåta dessa ytterligare intervall.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kan jag använda Azure dev Spaces på AKS-kluster med begränsad utgående trafik för klusternoder?

Ja, du kan använda Azure dev Spaces på AKS-kluster med [begränsad utgående trafik för klusternoder][aks-restrict-egress-traffic] aktiverade när följande FQDN har tillåtits:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Hämta Linux Alpine och andra Azure dev Spaces-bilder |
| gcr.io | HTTP: 443 | Hämta Helm/till-avbildningar |
| storage.googleapis.com | HTTP: 443 | Hämta Helm/till-avbildningar |
| azds-<guid>.<location>. azds.io | HTTPS:443 | För att kommunicera med Azure dev Spaces-backend-tjänster för din kontrollant. Du hittar exakt FQDN i "dataplaneFqdn" i% USERPROFILE%\.azds\settings.JSON |


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md