---
title: Vanliga frågor och svar om Azure dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Hitta svar på några vanliga frågor om Azure dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 317a942f94f3852cc11b8c0f004ed55097d05bc6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014208"
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

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan jag använda Azure dev Spaces på AKS-kluster med autentiserade IP-adressintervall för API-servern?

Ja, du kan använda Azure dev Spaces på AKS-kluster med [autentiserade IP-adressintervall för API-servern][aks-auth-range] aktiverat. När du [skapar][aks-auth-range-create] klustret måste du [tillåta ytterligare intervall baserat på din region][aks-auth-range-ranges]. Du kan också [Uppdatera][aks-auth-range-update] och befintliga kluster för att tillåta dessa ytterligare intervall.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md