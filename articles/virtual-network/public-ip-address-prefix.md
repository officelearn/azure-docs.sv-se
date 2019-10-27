---
title: Prefix för offentliga Azure-IP-adresser | Microsoft Docs
description: Lär dig mer om vad ett prefix för Azures offentliga IP-adress är och hur det kan hjälpa dig att tilldela förutsägbara offentliga IP-adresser till dina resurser.
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
ms.openlocfilehash: 964bc915347d11e087da0b34a8d4160d807a4158
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965402"
---
# <a name="public-ip-address-prefix"></a>Prefix för offentliga IP-adresser

Ett offentligt IP-adressprefix är ett reserverat intervall med IP-adresser för dina offentliga slut punkter i Azure. Azure allokerar ett sammanhängande intervall med adresser till din prenumeration utifrån hur många som du anger. Om du inte är bekant med offentliga adresser, se [offentliga IP-adresser.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Offentliga IP-adresser tilldelas från en adresspool med adresser i varje Azure-region. Du kan [Ladda ned](https://www.microsoft.com/download/details.aspx?id=56519) listan över intervall som Azure använder för varje region. Till exempel är 40.121.0.0/16 en av över 100 intervall som Azure använder i regionen USA, östra. Intervallet omfattar de användbara adresserna för 40.121.0.1-40.121.255.254.

Du skapar ett offentligt IP-adressprefix i en Azure-region och-prenumeration genom att ange ett namn och hur många adresser du vill att prefixet ska innehålla. Om du till exempel skapar ett offentligt IP-adressprefix på/28 allokerar Azure 16 adresser från ett av intervallen åt dig. Du vet inte vilket intervall Azure ska tilldela förrän du skapar intervallet, men adresserna är sammanhängande. Prefix för offentliga IP-adresser har en avgift. Mer information finns i [pris information om offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Varför ska du skapa ett offentligt IP-adressprefix?

När du skapar offentliga IP-adressresurser tilldelar Azure en tillgänglig offentlig IP-adress från något av de intervall som används i regionen. När Azure tilldelar adressen, vet du vad adressen är, men tills Azure tilldelar adressen, vet du inte vilken adress som kan tilldelas. Detta kan vara problematiskt när du, till exempel eller din affärs partner, ställer in brand Väggs regler som tillåter specifika IP-adresser. Varje gången du tilldelar en ny offentlig IP-adress till en resurs måste adressen läggas till i brand Väggs regeln. När du tilldelar adresser till resurser från ett offentligt IP-adressprefix behöver brand Väggs regler inte uppdateras varje gång du tilldelar en av adresserna, eftersom hela intervallet kan läggas till i en regel.

## <a name="benefits"></a>Erbjudande

- Du kan skapa offentliga IP-adressresurser från ett känt intervall.
- Du eller dina affärs partner kan skapa brand Väggs regler med intervall som innehåller offentliga IP-adresser som du har tilldelat, samt adresser som du inte har tilldelat ännu. Detta eliminerar behovet av att ändra brand Väggs regler när du tilldelar IP-adresser till nya resurser.
- Standard storleken för ett intervall du kan skapa är/28 eller 16 IP-adresser.
- Det finns inga begränsningar för hur många intervall du kan skapa, men det finns gränser för det högsta antalet statiska offentliga IP-adresser som du kan ha i en Azure-prenumeration. Det innebär att antalet intervall som du skapar inte kan omfatta fler statiska offentliga IP-adresser än du kan ha i din prenumeration. Mer information finns i [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- De adresser som du skapar med hjälp av adresser från prefixet kan tilldelas till alla Azure-resurser som du kan tilldela en offentlig IP-adress till.
- Du kan enkelt se vilka IP-adresser som är allokerade och ännu inte allokerade inom intervallet.

## <a name="scenarios"></a>Scenarier
Du kan koppla följande resurser till en statisk offentlig IP-adress från ett prefix:

|Resurs|Scenario|Steg|
|---|---|---|
|Virtual Machines| Att associera offentliga IP-adresser från ett prefix till dina virtuella datorer i Azure minskar hanterings kostnaderna när den kommer till vit listning IP-adresser i en brand vägg. Du kan enkelt vitlista ett helt prefix med en enda brand Väggs regel. När du skalar med virtuella datorer i Azure kan du koppla IP-adresser från samma prefix sparar kostnad, tid och hanterings kostnader.| Koppla IP-adresser från ett prefix till den virtuella datorn: 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. [Koppla IP-adressen till den virtuella datorns nätverks gränssnitt.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Standard belastnings utjämning | Om du kopplar offentliga IP-adresser från ett prefix till klient delens IP-konfiguration eller utgående regel för en Load Balancer säkerställer du förenkling av ditt offentliga IP-adressutrymme i Azure. Du kan förenkla ditt scenario genom att rensa utgående anslutningar som kommer från en uppsättning sammanhängande IP-adresser som definieras av offentliga IP-prefix. | Koppla IP-adresser från ett prefix till belastningsutjämnaren: 1. [Skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du skapar Load Balancer väljer eller uppdaterar du IP-adressen som skapades i steg 2 ovan som klient delens IP-adress för din Load Balancer. |
| Azure Firewall | Du kan använda en offentlig IP-adress från ett prefix för utgående SNAT. Det innebär att all utgående virtuell nätverks trafik översätts till den offentliga IP-adressen i [Azure-brandväggen](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Eftersom den här IP-adressen kommer från ett förinställt prefix är det mycket enkelt att veta i förväg vad din offentliga IP-adress i Azure kommer att se ut. | 1. [skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du [distribuerar Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)måste du se till att välja den IP-adress som du tidigare tilldelade från prefixet.|
| Application Gateway v2 | Du kan använda en offentlig IP-adress från ett prefix för den automatiska skalningen och den Zone-redundanta Application Gateway v2. Eftersom den här IP-adressen kommer från ett förinställt prefix är det mycket enkelt att veta i förväg vad din offentliga IP-adress i Azure kommer att se ut. | 1. [skapa ett prefix.](manage-public-ip-address-prefix.md) 2. [Skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) 3. När du [distribuerar Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway)måste du välja den IP-adress som du tidigare tilldelade från prefixet.|

## <a name="constraints"></a>Villkor

- Du kan inte ange IP-adresser för prefixet. Azure allokerar IP-adresserna för prefixet baserat på den storlek som du anger.
- Du kan skapa ett prefix med upp till 16 IP-adresser eller en/28. Mer information finns i [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Du kan inte ändra intervallet när du har skapat prefixet.
- Endast statiska offentliga IP-adresser som skapats med standard-SKU: n kan tilldelas från prefixets intervall. Mer information om offentliga IP-adresser finns i [offentlig IP-adress](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresser från intervallet kan bara tilldelas Azure Resource Manager resurser. Adresser kan inte tilldelas till resurser i den klassiska distributions modellen.
- Alla offentliga IP-adresser som skapas från prefixet måste finnas i samma Azure-region och prenumeration som prefixet, och måste tilldelas till resurser i samma region och prenumeration.
- Du kan inte ta bort ett prefix om några adresser i det har tilldelats offentliga IP-adressresurser som är kopplade till en resurs. Koppla bort alla offentliga IP-adressresurser som tilldelas IP-adresser från prefixet först.


## <a name="next-steps"></a>Nästa steg

- [Skapa](manage-public-ip-address-prefix.md) ett offentligt IP-adressprefix
