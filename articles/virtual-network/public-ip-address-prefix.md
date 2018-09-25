---
title: Azure offentlig IP-adressprefix | Microsoft Docs
description: Läs mer om vad en Azure offentlig IP-adressprefixet är och hur du tilldelar förutsägbara offentliga IP-adresser till dina resurser.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 5bbe0709f89ca198b0571526291f700c99e9e59f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966834"
---
# <a name="public-ip-address-prefix"></a>Offentliga IP-adressprefix

En offentlig IP-adressprefixet är ett reserverat intervall med IP-adresser för din offentliga slutpunkter i Azure. Azure tilldelar ett sammanhängande intervall med adresser till din prenumeration baserat på hur många som du anger. Om du inte är bekant med offentliga adresser [offentliga IP-adresser.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Offentliga IP-adresser tilldelas från en pool med adresser i varje Azure-region. Du kan [hämta](https://www.microsoft.com/download/details.aspx?id=41653) lista med intervall som använder Azure för varje region. Till exempel är 40.121.0.0/16 ett av fler än 100 intervall som använder Azure i östra USA. Intervallet omfattar användbara adresser av 40.121.0.1 - 40.121.255.254.

Du skapar en offentlig IP-adressprefix i ett Azure-region och prenumeration genom att ange ett namn och hur många adresser som du vill att prefixet som ska ingå. Till exempel om du skapar en offentlig IP-adressprefix på/28, tilldelar Azure 16 adresser från en av dess intervall för dig. Du vet inte vilka adressintervall som Azure tilldelar förrän du har skapat intervallet, men adresserna som är sammanhängande. Offentliga IP-adressprefix har en avgift. Mer information finns i [prissättning för offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

> [!IMPORTANT]
> Offentliga IP-Prefix är i en offentlig förhandsversion i begränsade regioner. Du kan [Lär dig vad det innebär att finnas i förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Offentliga IP-prefix är för närvarande tillgängligt i: västra centrala USA, västra USA, västra USA 2, centrala USA, Nordeuropa, Västeuropa och Sydostasien. En uppdaterad lista över regioner finns [Azure-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="why-create-a-public-ip-address-prefix"></a>Varför skapa en offentlig IP-adressprefix?

När du skapar offentliga IP-adressresurser tilldela Azure en tillgänglig offentliga IP-adress från något av de intervall som används i en annan region. När Azure tilldelar adress, vet du vilken adressen är dock tills Azure tilldelar adress, du vet inte vilken adress kan tilldelas. Detta kan vara problematiskt när, till exempel du eller dina affärspartner konfigurera brandväggsregler som tillåter specifika IP-adresser. Varje gång som du tilldelar en ny offentlig IP-adress till en resurs har adressen som ska läggas till brandväggsregeln. När du tilldelar adresser till dina resurser från en offentlig IP-adressprefix behöver inte uppdateras varje gång som du tilldelar en av adresserna, eftersom alla kunde läggas till en regel brandväggsregler.

## <a name="benefits"></a>Fördelar

- Du kan skapa offentliga IP-adress-resurser från flera kända.
- Du eller din affärspartner kan skapa brandväggsregler med intervall som innehåller offentliga IP-adresser som du har för närvarande tilldelat, samt adresser som du inte har tilldelats ännu. Detta eliminerar behovet av att ändra brandväggsregler när du tilldelar IP-adresser till nya resurser.
- Standardstorleken för ett intervall som du kan skapa/28 eller 16 IP-adresser.
- Det finns ingen gräns för hur många intervall som du kan skapa, men det finns gränser för det maximala antalet statiska offentliga IP-adresser som du kan ha i en Azure-prenumeration. Därför kan kan inte antalet intervall som du skapar omfatta flera statiska offentliga IP-adresser än vad du kan ha i din prenumeration. Mer information finns i [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- De adresser som du skapar med prefixet-adresser kan tilldelas till alla Azure-resurser som du kan tilldela en offentlig IP-adress till.
- Du kan enkelt se vilka IP-adresser som allokerats och ännu inte kopplats inom intervallet.

## <a name="scenarios"></a>Scenarier
Du kan associera följande resurser med en statisk offentlig IP-adress från ett prefix:

|Resurs|Scenario|Steg|
|---|---|---|
|Virtuella datorer| Associera offentliga IP-adresser från ett prefix till dina virtuella datorer i Azure minskar hanteringskostnader när det gäller att lista över tillåtna IP-adresser i brandväggen. Du kan bara godkänna ett hela prefix med en enda brandväggsregel. När du skalar med virtuella datorer i Azure kan du associera IP-adresser från samma prefix sparar kostnader, tid och omkostnader för lagringshantering.| Associera IP-adresser från ett prefix till den virtuella datorn: 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. [Associera IP-Adressen till den virtuella datorns nätverksgränssnitt.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Lastbalanserare | Associera offentliga IP-adresser från ett prefix till frontend-IP säkerställer konfiguration eller utgående regel för belastningsutjämning förenkling av distribution av ditt Azure offentlig IP-adressutrymme. Du kan förenkla din situation genom rensning utgående anslutningar till har sitt ursprung från en mängd sammanhängande IP-adresser som definieras av den offentliga IP-prefix. | Associera IP-adresser från ett prefix i Load balancer: 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du skapar belastningsutjämnaren, Välj eller uppdatera IP-Adressen som skapades i steg 2 ovan som frontend-IP för belastningsutjämnaren. |
| Azure Firewall | Du kan använda en offentlig IP-adress från ett prefix för utgående SNAT. Det innebär att all utgående trafik i virtuella nätverk översätts till den [Azure brandvägg](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) offentlig IP-adress. Eftersom denna IP-adress kommer från ett förutbestämt prefix, är det mycket enkelt förväg veta hur dina offentliga IP-fotavtryck i Azure ser ut. | 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du [distribuera Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), se till att välja den IP-Adressen du previosuly allokeras från prefixet.|

## <a name="constraints"></a>Villkor

- Du kan inte ange IP-adresser för prefixet. Azure tilldelar IP-adresser för prefixet, baserat på den storlek som du anger.
- Du kan inte ändra intervallet när du har skapat prefixet.
- Intervallet är för IPv4-adresser. Intervallet innehåller inte IPv6-adresser.
- Endast statiska offentliga IP-adresser skapas med Standard-SKU kan tilldelas från adressintervallet för det prefixet. Läs mer om offentliga IP-adress SKU: er i [offentliga IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresserna från intervallet kan endast tilldelas till Azure Resource Manager-resurser. Adresser kan inte tilldelas till resurser i den klassiska distributionsmodellen.
- Alla offentliga IP-adresser som skapats från prefixet måste finnas i samma Azure-region och prenumeration som prefix och måste tilldelas till resurser i samma region och prenumeration.
- Du kan inte ta bort ett prefix om alla adresser i den tilldelas till offentliga IP-adressresurser som är kopplad till en resurs. Koppla bort alla offentliga IP-adressresurser som har tilldelats IP-adresser från prefixet först.


## <a name="next-steps"></a>Nästa steg

- [Skapa](manage-public-ip-address-prefix.md) en offentlig IP-adressprefix
