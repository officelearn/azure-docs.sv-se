---
title: Vad är en Azure Network Access Control-lista?
description: Lär dig mer om åtkomst kontrol listor i Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 38655a9da103d1d669f87c6195be7f17702f9348
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056680"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Vad är en slut punkts åtkomst kontrol lista?

> [!IMPORTANT]
> Azure har två olika [distributions modeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder distributions modellen för Resource Manager. 

En slut punkts åtkomst kontrol lista (ACL) är en säkerhets förbättring som är tillgänglig för Azure-distributionen. En ACL ger möjlighet att selektivt tillåta eller neka trafik för en virtuell dator slut punkt. Den här paket filtrerings funktionen ger ett extra säkerhets lager. Du kan bara ange nätverks-ACL: er för slut punkter. Du kan inte ange en ACL för ett virtuellt nätverk eller ett särskilt undernät som finns i ett virtuellt nätverk. Vi rekommenderar att du använder nätverks säkerhets grupper (NSG: er) i stället för ACL: er, närhelst det är möjligt. När du använder NSG: er ersätts slut punkts åtkomst kontrol listan och används inte längre. Mer information om NSG: er finns i [Översikt över nätverks säkerhets grupper](security-overview.md)

ACL: er kan konfigureras med hjälp av antingen PowerShell eller Azure Portal. Information om hur du konfigurerar en nätverks-ACL med hjälp av PowerShell finns i [Hantera åtkomst kontrol listor för slut punkter med PowerShell](virtual-networks-acl-powershell.md). Information om hur du konfigurerar en nätverks-ACL med hjälp av Azure Portal finns i [så här konfigurerar du slut punkter på en virtuell dator](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Med hjälp av nätverks-ACL: er kan du göra följande:

* Selektivt tillåta eller neka inkommande trafik baserat på IPv4-adress intervall för fjärr-undernät till en slut punkt för indata för virtuell dator.
* Svartlista IP-adresser
* Skapa flera regler per virtuell dator slut punkt
* Använd regel ordning för att se till att rätt regel uppsättning tillämpas på en angiven virtuell dator slut punkt (från lägsta till högsta)
* Ange en ACL för en speciell IPv4-adress för fjärr-undernät.

Se artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) för ACL-gränser.

## <a name="how-acls-work"></a>Så här fungerar ACL: er
En ACL är ett objekt som innehåller en lista med regler. När du skapar en ACL och använder den på en virtuell dators slut punkt sker paket filtreringen på noden värd för den virtuella datorn. Det innebär att trafiken från fjärr-IP-adresser filtreras efter noden värd för matchande ACL-regler i stället för på den virtuella datorn. Detta förhindrar att den virtuella datorn tillbringar de ädel CPU-cyklerna i paket filtrering.

När en virtuell dator skapas placeras en standard-ACL på plats för att blockera all inkommande trafik. Men om en slut punkt skapas för (port 3389), ändras standard-ACL: en för att tillåta all inkommande trafik för den slut punkten. Inkommande trafik från ett fjärran slutet undernät tillåts sedan till den slut punkten och ingen brand Väggs etablering krävs. Alla andra portar blockeras för inkommande trafik om inte slut punkter skapas för dessa portar. Utgående trafik tillåts som standard.

**Exempel på standard-ACL-tabell**

| **Allmänhet #** | **Fjärrundernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Hastigheter |

## <a name="permit-and-deny"></a>Tillåt och neka
Du kan selektivt tillåta eller neka nätverks trafik för en slut punkt för indatakälla för virtuella datorer genom att skapa regler som anger "perm" eller "Deny". Det är viktigt att Observera att när en slut punkt skapas tillåts all trafik till slut punkten. Av den anledningen är det viktigt att förstå hur du skapar regler för att tillåta/neka och placera dem i rätt ordning om du vill ha detaljerad kontroll över nätverks trafiken som du väljer för att få åtkomst till den virtuella datorns slut punkt.

Saker att tänka på:

1. **Ingen ACL –** Som standard när en slut punkt skapas tillåter vi alla för slut punkten.
2. **Tillåt-** När du lägger till ett eller flera "Permit"-intervall, så nekar du alla andra intervall som standard. Endast paket från det tillåtna IP-intervallet kommer att kunna kommunicera med den virtuella dator slut punkten.
3. **Neka-** När du lägger till ett eller flera "Neka"-intervall tillåter du alla andra trafik intervall som standard.
4. **Kombination av tillstånd och neka-** Du kan använda en kombination av "Tillåt" och "Neka" när du vill dela ut ett särskilt IP-adressintervall som ska tillåtas eller nekas.

## <a name="rules-and-rule-precedence"></a>Regler och regel prioritet
Nätverks-ACL: er kan konfigureras för vissa slut punkter för virtuella datorer. Du kan till exempel ange en nätverks-ACL för en RDP-slutpunkt som skapats på en virtuell dator som låser ned åtkomsten för vissa IP-adresser. Tabellen nedan visar ett sätt att bevilja åtkomst till offentliga virtuella IP-adresser (VIP) för ett visst intervall för att tillåta åtkomst för RDP. Alla andra fjärr-IP-adresser nekas. Vi följer regel ordningen för *lägsta prioritet* .

### <a name="multiple-rules"></a>Flera regler
Om du i exemplet nedan bara vill tillåta åtkomst till RDP-slutpunkten från två offentliga IPv4-adressintervall (65.0.0.0/8, och 159.0.0.0/8) kan du uppnå detta genom att ange två *tillstånds* regler. I det här fallet, eftersom RDP skapas som standard för en virtuell dator, kanske du vill låsa åtkomsten till RDP-porten baserat på ett fjärran slutet undernät. Exemplet nedan visar ett sätt att bevilja åtkomst till offentliga virtuella IP-adresser (VIP) för ett visst intervall för att tillåta åtkomst för RDP. Alla andra fjärr-IP-adresser nekas. Detta fungerar eftersom nätverks-ACL: er kan konfigureras för en speciell slut punkt för virtuella datorer och åtkomst nekas som standard.

**Exempel – flera regler**

| **Allmänhet #** | **Fjärrundernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Hastigheter |
| 200 |159.0.0.0/8 |3389 |Hastigheter |

### <a name="rule-order"></a>Regel ordning
Eftersom det går att ange flera regler för en slut punkt måste det finnas ett sätt att ordna regler för att avgöra vilken regel som prioriteras. Regel ordningen anger prioritet. Nätverks-ACL: er följer regel ordningen för *lägsta prioritet* . I exemplet nedan beviljas slut punkten på port 80 selektivt åtkomst till vissa IP-adressintervall. För att konfigurera detta har vi en regel för att neka \# (regel 100) för adresser i 175.1.0.1/24-utrymmet. En andra regel anges sedan med prioritet 200 som tillåter åtkomst till alla andra adresser under 175.0.0.0/8.

**Exempel – regel prioritet**

| **Allmänhet #** | **Fjärrundernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Neka |
| 200 |175.0.0.0/8 |80 |Hastigheter |

## <a name="network-acls-and-load-balanced-sets"></a>Nätverks-ACL: er och belastningsutjämnade uppsättningar
Nätverks-ACL: er kan anges i en belastningsutjämnad uppsättnings slut punkt. Om en ACL anges för en belastningsutjämnad uppsättning tillämpas nätverks-ACL: en på alla virtuella datorer i den belastningsutjämnade uppsättningen. Om t. ex. en belastningsutjämnad uppsättning skapas med "port 80" och den belastningsutjämnade uppsättningen innehåller tre virtuella datorer, kommer nätverks åtkomst kontrol listan som skapades på slut punkten "port 80" för en virtuell dator automatiskt att gälla för de andra virtuella datorerna.

![Nätverks-ACL: er och belastningsutjämnade uppsättningar](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Nästa steg
[Hantera åtkomst kontrol listor för slut punkter med PowerShell](virtual-networks-acl-powershell.md)

