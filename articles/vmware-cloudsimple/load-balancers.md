---
title: Azure VMware-lösningar (AVS) – Välj en lösning för belastnings utjämning för moln privata moln
description: Beskriver alternativen för belastnings utjämning som distribuerar ett program i ett moln privat moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014886"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Välj en lösning för belastnings utjämning för moln privata moln

När du distribuerar ett program i ett moln i molnet kan du välja något av flera alternativ för belastnings utjämning.

Du kan välja en virtuell eller programvarubaserad belastningsutjämnare i ditt moln privata moln eller även använda Azure L7-belastningsutjämnaren som körs i din Azure-prenumeration på klient sidan för virtuella datorer som körs i det privata molnet i AVS-molnet. Här listas några alternativ:

## <a name="virtual-load-balancers"></a>Virtuella belastningsutjämnare

Du kan distribuera virtuella belastningsutjämnare i VMware-miljön via vCenter-gränssnittet och konfigurera dem för att placera program trafiken på klient sidan.

Några populära leverantörer är: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7-belastningsutjämnare

När du använder Azure Application Gateway som en L7-belastningsutjämnare för ditt program som körs i ett moln privat moln, behöver du inte hantera program varan för belastningsutjämnare. Belastnings Utjämnings program varan hanteras av Azure. Alla virtuella datorer i det privata moln molnet använder privata IP-adresser och behöver inte fler NAT-regler eller offentliga IP-adresser för att matcha namn. Virtuella datorer i virtuella datorer kommunicerar med Azure Application Gateway över en privat, låg latens-anslutning med hög bandbredd.

Mer information om hur du konfigurerar den här lösningen finns i lösnings guiden för att använda Azure Application Gateway som en L7-belastningsutjämnare.

## <a name="azure-internal-load-balancer"></a>Intern Azure-belastningsutjämnare

Om du väljer att köra programmet i en hybrid distribution där front webb nivån körs i ett Azure vNet i din Azure-prenumeration och databas nivån för programmet körs i virtuella VMware-datorer i det privata AVS-molnet kan du använda Azure Internal Load Balancer (L 4 Load Balancer) framför dina virtuella datorer i DB-nivån för trafik hantering.

Mer information finns i dokumentationen till Azure [intern Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) .

## <a name="global-server-load-balancer"></a>Global Server belastnings utjämning

Om du letar efter en DNS-baserad belastningsutjämnare kan du antingen använda lösningar från tredje part som är tillgängliga på Azure Marketplace eller gå med i den interna Azure-lösningen.

Azure Traffic Manager är en DNS-baserad trafikbelastnings utjämning som gör att du kan distribuera trafik optimalt till tjänster i globala Azure-regioner och lokalt, samtidigt som du ger hög tillgänglighet och svars tider. Mer information finns i Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) -dokumentationen.
