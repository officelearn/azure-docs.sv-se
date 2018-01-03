---
title: "Azure-nätverk för behållare | Microsoft Docs"
description: "Läs mer om CNI plugin-program för Kubernetes kluster, vilket gör att behållare att kommunicera med varandra och andra resurser i ett virtuellt nätverk."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Behållaren nätverk

Azure tillhandahåller en [behållare nätverk gränssnitt (CNI) plugin](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) som gör det möjligt att distribuera och hantera din egen Kubernetes-kluster med interna Azure nätverkskapacitet. Plugin-programmet är aktiverat, som standard när du distribuerar Kubernetes kluster med den [Azure Container Service Engine](https://github.com/Azure/acs-engine) (eller ACS-motorn).

## <a name="networking-capabilities"></a>Funktioner för nätverk

Behållare kan använda stort utbud av funktioner som ett virtuellt nätverk erbjuder som:
-   Du kan skapa ett separat virtuellt nätverk för klustret eller distribuera klustret i ett befintligt virtuellt nätverk. 
-   Varje baljor i klustret tar emot en IP-adress från det virtuella nätverket och kan kommunicera direkt med andra skida i klustret och en virtuell dator i det virtuella nätverket. 
-   En baljor kan ansluta till andra skida och virtuella datorer i peerkoppla virtuella nätverk och lokala nätverk via ExpressRoute och plats-till-plats VPN-anslutningar. Lokala resurser kan kommunicera med skida. 
-   Du kan exponera en Kubernetes tjänst till Internet via Azure belastningsutjämnare.  
-   Skida i ett undernät som har aktiverat slutpunkter på ett säkert sätt kan ansluta till Azure-tjänster (lagring och SQL-databas, till exempel).
-   Du kan använda användardefinierade vägar för att dirigera trafik från skida till en virtuell nätverksenhet. 
-   Skida kan komma åt offentliga resurser på Internet.
-   Du kan tilldela en baljor en offentlig IP-adress som kan associeras med ett DNS-namn.
 
## <a name="limits"></a>Begränsningar
Du kan distribuera upp till 4 000 noder i ett kluster med Kubernetes och upp till 250 skida per nod med en övergripande gräns på 16 000 skida per kluster, när du använder plugin-programmet.

## <a name="constraints"></a>Villkor
- Plugin-programmet är inte aktiverad när du distribuerar ett Kubernetes kluster med den [Azure Container Service (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kluster med Kubernetes.
- Det finns inget inbyggt stöd för principer för Kubernetes nätverk, inklusive DNS- eller principer.
- Plugin-programmet stöder inte per baljor nätverksprinciper.

## <a name="pricing"></a>Prissättning
Det är gratis för att använda CNI plugin-programmet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera ett kluster Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) i din [egna virtuella nätverk](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
