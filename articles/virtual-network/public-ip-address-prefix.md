---
title: Prefix för Azure Public IP-adress | Microsoft-dokument
description: Lär dig mer om vad ett Azure-offentligt IP-adressprefix är och hur det kan hjälpa dig att tilldela förutsägbara offentliga IP-adresser till dina resurser.
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
ms.openlocfilehash: 12fb7e03062600745cd8511d37b439ce44f2ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640728"
---
# <a name="public-ip-address-prefix"></a>Prefix för offentliga IP-adresser

Ett offentligt IP-adressprefix är ett reserverat intervall med IP-adresser för dina offentliga slutpunkter i Azure. Azure allokerar ett sammanhängande intervall med adresser till din prenumeration baserat på hur många du anger. Om du inte är bekant med offentliga adresser läser du [Offentliga IP-adresser.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Offentliga IP-adresser tilldelas från en pool med adresser i varje Azure-region. Du kan [hämta](https://www.microsoft.com/download/details.aspx?id=56519) listan över intervall som Azure använder för varje region. Till exempel är 40.121.0.0/16 ett av över 100 intervall som Azure använder i regionen östra USA. I sortimentet ingår användbara adresser på 40.121.0.1 - 40.121.255.254.

Du skapar ett offentligt IP-adressprefix i en Azure-region och prenumeration genom att ange ett namn och hur många adresser du vill att prefixet ska inkludera. Om du till exempel skapar ett offentligt IP-adressprefix på /28 allokerar Azure 16 adresser från ett av dess intervall för dig. Du vet inte vilket intervall Azure tilldelar förrän du skapar intervallet, men adresserna är sammanhängande. Prefix för offentlig IP-adress har en avgift. Mer information finns i [prissättningen för offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Varför skapa ett offentligt IP-adressprefix?

När du skapar offentliga IP-adressresurser tilldelar Azure en tillgänglig offentlig IP-adress från något av de intervall som används i regionen. När Azure tilldelar adressen vet du vad adressen är, men tills Azure tilldelar adressen vet du inte vilken adress som kan tilldelas. Detta kan vara problematiskt när du eller dina affärspartners ställer in brandväggsregler som tillåter specifika IP-adresser. Varje gång du tilldelar en ny offentlig IP-adress till en resurs måste adressen läggas till i brandväggsregeln. När du tilldelar adresser till dina resurser från ett offentligt IP-adressprefix behöver brandväggsregler inte uppdateras varje gång du tilldelar en av adresserna, eftersom hela intervallet kan läggas till i en regel.

## <a name="benefits"></a>Fördelar

- Du kan skapa offentliga IP-adressresurser från ett känt intervall.
- Du eller dina affärspartners kan skapa brandväggsregler med intervall som innehåller offentliga IP-adresser som du för närvarande har tilldelat, samt adresser som du inte har tilldelat ännu. Detta eliminerar behovet av att ändra brandväggsregler när du tilldelar IP-adresser till nya resurser.
- Standardstorleken för ett intervall som du kan skapa är /28 eller 16 IP-adresser.
- Det finns inga gränser för hur många intervall du kan skapa, men det finns gränser för det maximala antalet statiska offentliga IP-adresser som du kan ha i en Azure-prenumeration. Därför kan antalet intervall som du skapar inte omfatta mer statiska offentliga IP-adresser än du kan ha i din prenumeration. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- De adresser som du skapar med hjälp av adresser från prefixet kan tilldelas alla Azure-resurser som du kan tilldela en offentlig IP-adress till.
- Du kan enkelt se vilka IP-adresser som allokeras och ännu inte allokerats inom intervallet.

## <a name="scenarios"></a>Scenarier
Du kan associera följande resurser till en statisk offentlig IP-adress från ett prefix:

|Resurs|Scenario|Steg|
|---|---|---|
|Virtuella datorer| Genom att associera offentliga IP-adresser från ett prefix till dina virtuella datorer i Azure minskar hanteringskostnaderna när det gäller vitlistning av IP-adresser i en brandvägg. Du kan helt enkelt vitlista ett helt prefix med en enda brandväggsregel. När du skalar med virtuella datorer i Azure kan du associera IP-adresser från samma prefix som sparar kostnad, tid och hanteringskostnader.| Så här associerar du IP-adresser från ett prefix till den virtuella datorn: 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. [Associera IP-adressen till den virtuella datorns nätverksgränssnitt.](virtual-network-network-interface-addresses.md#add-ip-addresses) Du kan också [associera IP-adresser till en skala uppsättning för virtuella datorer](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standard belastningsutjämnare | Genom att associera offentliga IP-adresser från ett prefix till ip-konfigurationen för klientdel eller utgående regeln för en belastningsutjämnare säkerställer förenklingen av ditt offentliga AZURE-IP-adressutrymme. Du kan förenkla scenariot genom att ha besparing av utgående anslutningar som ska komma från ett intervall med sammanhängande IP-adresser som definieras av offentliga IP-prefix. | Så här associerar du IPs från ett prefix till din belastningsutjämnare: 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du skapar belastningsutjämnaren väljer eller uppdaterar du IP-adressen som skapas i steg 2 ovan som frontend-IP för din belastningsutjämnare. |
| Azure Firewall | Du kan använda en offentlig IP från ett prefix för utgående SNAT. Det innebär att all utgående virtuell nätverkstrafik översätts till den offentliga [IP-adressen för Azure Firewall.](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Eftersom den här IP:en kommer från ett förutbestämt prefix är det mycket lätt att veta i förväg hur ditt offentliga IP-fotavtryck i Azure kommer att se ut. | 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du [distribuerar Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)måste du välja den IP som du tidigare har tilldelat från prefixet.|
| Application Gateway v2 | Du kan använda en offentlig IP från ett prefix för automatisk skalning och zonundant programgateway v2. Eftersom den här IP:en kommer från ett förutbestämt prefix är det mycket lätt att veta i förväg hur ditt offentliga IP-fotavtryck i Azure kommer att se ut. | 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du [distribuerar Programgatewayen](../application-gateway/quick-create-portal.md#create-an-application-gateway)måste du välja den IP som du tidigare har tilldelat från prefixet.|

## <a name="constraints"></a>Villkor

- Du kan inte ange IP-adresserna för prefixet. Azure allokerar IP-adresserna för prefixet, baserat på den storlek som du anger.
- Du kan skapa ett prefix på upp till 16 IP-adresser eller en /28. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Du kan inte ändra intervallet när du har skapat prefixet.
- Endast statiska offentliga IP-adresser som skapats med standard-SKU kan tilldelas från prefixets intervall. Mer information om offentliga IP-adressSku:er finns i [offentlig IP-adress](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresser från intervallet kan endast tilldelas Azure Resource Manager-resurser. Adresser kan inte tilldelas resurser i den klassiska distributionsmodellen.
- Alla offentliga IP-adresser som skapas från prefixet måste finnas i samma Azure-region och prenumeration som prefixet och måste tilldelas resurser i samma region och prenumeration.
- Du kan inte ta bort ett prefix om några adresser i det har tilldelats offentliga IP-adressresurser som är associerade till en resurs. Ta bort alla offentliga IP-adressresurser som tilldelas IP-adresser från prefixet först.


## <a name="next-steps"></a>Nästa steg

- [Skapa](manage-public-ip-address-prefix.md) ett offentligt IP-adressprefix
