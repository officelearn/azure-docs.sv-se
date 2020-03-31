---
title: Azure VMware Solution by CloudSimple - Välj en belastningsutjämningslösning för CloudSimple Private Clouds
description: Beskriver alternativen för belastningsutjämning som distribuerar ett program i ett privat moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014886"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Välj en belastningsutjämningslösning för CloudSimple Private Clouds

När du distribuerar ett program i ett CloudSimple Private Cloud kan du välja något av flera alternativ för belastningsutjämning.

Du kan välja en virtuell eller programvarubaserad belastningsutjämnare i ditt CloudSimple privata moln eller till och med använda Azure L7-belastningsutjämnare som körs i din Azure-prenumeration för att frontend-datorerna på webbnivå som körs i CloudSimple Private Cloud. Här listar vi några alternativ:

## <a name="virtual-load-balancers"></a>Virtuella belastningsutjämnare

Du kan distribuera virtuella belastningsutjämnare i din VMware-miljö via vCenter-gränssnittet och konfigurera dem så att programtrafiken fronten avslutas.

Några populära leverantörer är: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 belastningsutjämnare

När du använder Azure Application Gateway som L7-belastningsutjämnare för ditt program som körs i ett privat moln behöver du inte hantera belastningsutjämnaren. Belastningsutjämnarens programvara hanteras av Azure. Alla virtuella webbnivå-datorer i det privata molnet använder privata IP-adresser och kräver inte ytterligare NAT-regler eller offentliga IP-adresser för att matcha namn. Virtuella datorer på webbnivå kommunicerar med Azure Application Gateway via en privat anslutning med låg latens med hög bandbredd.

Mer information om hur du konfigurerar den här lösningen finns i lösningsguiden för att använda Azure Application Gateway som L7-belastningsutjämnare.

## <a name="azure-internal-load-balancer"></a>Azure intern belastningsutjämnare

Om du väljer att köra ditt program i en hybriddistribution där webbfrontdnivån körs inom ett Azure-vNet i din Azure-prenumeration och DB-nivån för programmet körs i virtuella datorer med VMware i CloudSimple Private Cloud, kan du använda Azure intern belastning balanseraren (L4 belastningsutjämnare) framför dina virtuella db-nivå-datorer för trafikhantering.

Mer information finns i Azures interna [dokumentation för belastningsutjämning.](../load-balancer/concepts-limitations.md#internalloadbalancer)

## <a name="global-server-load-balancer"></a>Global belastningsutjämnare för servrar

Om du letar efter en DNS-baserad belastningsutjämnare kan du antingen använda tredjepartslösningar som är tillgängliga på Azure Marketplace eller gå med den inbyggda Azure-lösningen.

Azure Traffic Manager är en DNS-baserad trafikbelastningsutjämningsfaktor som gör att du kan distribuera trafik optimalt till tjänster i globala Azure-regioner och lokala, samtidigt som du tillhandahåller hög tillgänglighet och svarstider. Mer information finns i Azure [Traffic Manager-dokumentationen.](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)
