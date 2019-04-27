---
title: Containernätverk med Azure Virtual Network | Microsoft Docs
description: Läs om hur du gör det möjligt för containrar att använda Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 2ebc678bffbbbe5d512d620b8f77ac0a245c0aff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713851"
---
# <a name="enable-containers-to-use-azure-virtual-network-capabilities"></a>Göra så att containrar kan använda Azure Virtual Network-funktioner

Hämta den omfattande uppsättningen med Azure-nätverksfunktioner till containrar genom att använda samma programvara som definierade nätverksstacken som används av virtuella datorer. Plugin-programmet Azure Virtual Network CNI (Container Network Interface) installeras på en virtuell Azure-dator. Plugin-programmet tilldelar IP-adresser från ett virtuellt nätverk för containrar som finns i den virtuella datorn, bifogar dem till det virtuella nätverket och ansluter dem sedan direkt till andra containrar och virtuella nätverksresurser. Plugin-programmet förlitar sig inte på överläggsnätverk eller vägar för anslutningen, och erbjuder samma prestanda som virtuella datorer. På en hög nivå innehåller plugin-programmet följande funktioner:

- En virtuell IP-adress tilldelas till varje podd, som kan bestå av en eller flera containrar.
- Poddar kan ansluta till peerkopplade virtuella nätverk och till lokala nätverk via ExpressRoute eller VPN för plats-till-plats. Poddar kan även nås från peerkopplade och lokala nätverk.
- Poddar kan komma åt tjänster som Azure Storage och Azure SQL Database, som skyddas av tjänstslutpunkter för virtuellt nätverk.
- Nätverkssäkerhetsgrupper och vägar kan tillämpas direkt på poddar.
- Poddar kan placeras direkt bakom en intern eller offentlig lastbalanserare i Azure, precis som virtuella datorer
- Poddar kan tilldelas en offentlig IP-adress, vilket gör dem direkt åtkomliga från internet. Poddar kan också ansluta till internet själva.
- Fungerar sömlöst med Kubernetes-resurser, till exempel tjänster, ingående domänkontrollanter och Kube DNS. En Kubernetes-tjänst kan också exponeras internt eller externt via Azure Load Balancer.

Följande bild visar hur plugin-programmet erbjuder Azure Virtual Network-funktioner för poddarna:

![Översikt över nätverk för containrar](./media/container-networking/container-networking-overview.png)

Plugin-programmet stöder både Linux- och Windows-plattformar.

## <a name="connecting-pods-to-a-virtual-network"></a>Ansluta poddar till ett virtuellt nätverk

Poddar finns i en virtuell dator som är en del av ett virtuellt nätverk. En pool med IP-adresser för poddarna har konfigurerats som sekundära adresser i en virtuell dators nätverksgränssnitt. Azure CNI konfigurerar grundläggande nätverksanslutning för poddar och hanterar användningen av IP-adresser i poolen. När en podd visas i den virtuella datorn tilldelar Azure CNI en tillgänglig IP-adress från poolen och ansluter podden till en brygga för programvara på den virtuella datorn. När en podd avslutas läggs IP-adressen tillbaka i poolen. Följande bild visar hur poddar ansluter till ett virtuellt nätverk:

![Information om nätverk för containrar](./media/container-networking/container-networking-detail.png)

## <a name="internet-access"></a>Internet-åtkomst

Om du vill aktivera poddåtkomst till internet konfigurerar plugin-programmet *iptables*-regler för att nätadressöversätta (NAT) internet-bunden trafik från poddar. IP-källadressen för paketet översätts till den primära IP-adressen på den virtuella datorns nätverksgränssnitt. Virtuella Windows-datorer identifierar automatiskt källan för NAT-trafik (SNAT) IP-adresser som är utanför det undernät som den virtuell datorn tillhör som mål. Normalt översätts all trafik till en IP-adress utanför IP-adressintervallet för det virtuella nätverket.

## <a name="limits"></a>Limits

Plugin-programmet stöder upp till 250 poddar per virtuell dator och upp till 16 000 poddar i ett virtuellt nätverk. Dessa gränser är olika för [Azure Kubernetes Service](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-kubernetes-service-limits).

## <a name="using-the-plug-in"></a>Använda plugin-programmet

Plugin-programmet kan användas på följande sätt för att tillhandahålla grundläggande anslutning av virtuellt nätverk för poddar eller Docker-containrar:

- **Azure Kubernetes Service**: Plugin-programmet är integrerad i Azure Kubernetes Service (AKS) och kan användas genom att välja den *avancerade nätverk* alternativet. Via avancerade nätverk kan du distribuera ett Kubernetes-kluster i ett befintligt eller nytt virtuellt nätverk. Mer information om avancerade nätverk och hur du konfigurerar dem finns avsnittet om[nätverkskonfiguration i AKS](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **AKS-Engine**: AKS-motorn är ett verktyg som genererar en Azure Resource Manager-mall för distribution av ett Kubernetes-kluster i Azure. Detaljerade anvisningar finns i [distribuera plugin-programmet för AKS-Engine Kubernetes-kluster](deploy-container-networking.md#deploy-the-azure-virtual-network-container-network-interface-plug-in).
- **Skapa din egen Kubernetes-kluster i Azure**: Plugin-programmet kan användas för att tillhandahålla grundläggande nätverk för Poddar i Kubernetes-kluster som du distribuerar själv, utan att behöva AKS eller verktyg som AKS-motorn. I det här fallet är plugin-programmet installerat och aktiverat på varje virtuell dator i ett kluster. Detaljerade anvisningar finns i avsnittet om att [distribuera plugin-programmet för ett Kubernetes-kluster som du distribuerar själv](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).
- **Virtuellt nätverk bifoga för Docker-behållare i Azure**: Plugin-programmet kan användas i fall där du inte vill skapa ett Kubernetes-kluster och vill skapa Docker-behållare med virtuellt nätverk ansluta på virtuella datorer. Detaljerade anvisningar finns i avsnittet om att [distribuera plugin-programmet för Docker](deploy-container-networking.md#deploy-plug-in-for-docker-containers).

## <a name="next-steps"></a>Nästa steg

[Distribuera plugin-programmet](deploy-container-networking.md) för Kubernetes-kluster eller Docker-containrar
