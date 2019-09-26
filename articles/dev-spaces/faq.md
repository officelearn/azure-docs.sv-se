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
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305982"
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


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md