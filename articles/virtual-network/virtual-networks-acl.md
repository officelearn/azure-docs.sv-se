---
title: "Vad är Azure network access control list?"
description: "Lär dig mer om åtkomstkontrollistor i Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 9a0c85367968c9b38104012d75b1f3975be82cc1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-an-endpoint-access-control-list"></a>Vad är en slutpunkt för åtkomstkontrollista?

> [!IMPORTANT]
> Azure har två olika [distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-distributionsmodellen. 

En slutpunkt åtkomstkontrollista (ACL) är en säkerhetsförbättring av för din Azure-distribution. En ACL ger möjlighet att tillåta eller neka trafik för en virtuell dator-slutpunkten. Funktionen paket filtrering ger ett ytterligare lager av säkerhet. Du kan ange nätverk ACL: er för endast slutpunkter. Du kan inte ange en ACL för ett virtuellt nätverk eller ett specifikt undernät som ingår i ett virtuellt nätverk. Det rekommenderas att använda nätverkssäkerhetsgrupper (NSG: er) i stället för ACL: er när det är möjligt. Läs mer om NSG: er i [nätverk Säkerhetsöversikt grupp](virtual-networks-nsg.md)

ACL: er kan konfigureras med hjälp av PowerShell eller Azure-portalen. Om du vill konfigurera ett ACL-nätverk med hjälp av PowerShell Se [hantera åtkomstkontrollistor för slutpunkter med hjälp av PowerShell](virtual-networks-acl-powershell.md). Om du vill konfigurera ett ACL-nätverk med hjälp av Azure portal finns [hur du ställer in slutpunkter till en virtuell dator](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Med nätverks-ACL: er kan göra du följande:

* Tillåta eller neka inkommande trafik baserat på fjärrundernät IPv4-adressintervall till slutpunkten för indata en virtuell dator.
* Svartlistat IP-adresser
* Skapa flera regler per virtuell datorslutpunkt
* Använd regel ordning för att säkerställa rätt uppsättning regler tillämpas på en viss virtuell datorslutpunkt (lägsta och högsta)
* Ange en ACL för en specifik fjärrundernät IPv4-adress.

Finns det [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) artikel för ACL-gränser.

## <a name="how-acls-work"></a>Så här fungerar ACL: er
En ACL är ett objekt som innehåller en lista över regler. När du skapar en ACL och tillämpa den på en virtuell datorslutpunkt sker filtrering av nätverkspaket på värdnoden på den virtuella datorn. Det betyder att trafiken från fjärranslutna IP-adresser filtreras efter värdnoden för matchande ACL-regler i stället för på den virtuella datorn. Detta förhindrar att den virtuella datorn utgifter värdefull CPU-cykler för filtrering av nätverkspaket.

När en virtuell dator skapas placeras standard ACL på plats för att blockera all inkommande trafik. Men om en slutpunkt har skapats för (port 3389), sedan standard ACL ändras för att tillåta all inkommande trafik för denna slutpunkt. Inkommande trafik från alla fjärrundernät tillåts sedan till denna slutpunkt och ingen brandvägg etablering krävs. Alla portar blockeras för inkommande trafik såvida inte slutpunkter har skapats för dessa portar. Utgående trafik tillåts som standard.

**Standard ACL exempeltabell**

| **Regel #** | **Fjärranslutet undernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Tillåt |

## <a name="permit-and-deny"></a>Tillåt och neka
Du kan selektivt tillåter eller nekar nätverkstrafik för en virtuell dator indataslutpunkten genom att skapa regler som anger ”Tillåt” eller ”neka”. Det är viktigt att notera att som standard när en slutpunkt skapas alla tillåts trafik till slutpunkten. Därför är det viktigt att förstå hur du skapar tillstånd/neka regler och placerar dem i rätt ordning prioritet om du vill granulär kontroll över nätverkstrafik som du väljer att tillåta för att nå slutpunkten för virtuell dator.

Saker att tänka på:

1. **Inga ACL –** som standard när en slutpunkt som vi bevilja alla för slutpunkten.
2. **Tillåt -** när du lägger till en eller flera ”Tillåt” intervall du neka alla intervall som standard. Endast paket från det tillåtna IP-adressintervallet kommer att kunna kommunicera med den virtuella datorslutpunkten.
3. **Neka -** när du lägger till en eller flera ”neka” adressintervall tillåter du alla andra områden av trafik som standard.
4. **Kombinationen av Tillåt och neka -** du kan använda en kombination av ”Tillåt” och ”neka” om du vill dela ut en specifik IP-adressintervall som ska beviljas eller nekas.

## <a name="rules-and-rule-precedence"></a>Regler och regeln företräde
ACL: er för nätverket kan ställas in på en specifik virtuell dator slutpunkter. Du kan till exempel ange ett nätverk ACL för en RDP-slutpunkt skapas på en virtuell dator som låser ned åtkomst för vissa IP-adresser. Tabellen nedan visar ett sätt att bevilja åtkomst till offentliga virtuella IP-adresser (VIP) för ett visst intervall som tillåter åtkomst för RDP. Alla andra fjärranslutna IP-adresser att nekas. Vi följer en *lägsta företräde* regel ordning.

### <a name="multiple-rules"></a>Flera regler
I exemplet nedan om du vill tillåta åtkomst till RDP-slutpunkt endast från två offentliga IPv4-adressintervall (65.0.0.0/8 och 159.0.0.0/8), du kan åstadkomma detta genom att ange två *tillåter* regler. Eftersom RDP skapas som standard för en virtuell dator, kan du i det här fallet vill låsa åtkomst till RDP-porten baserat på ett fjärranslutet undernät. Exemplet nedan visar ett sätt att bevilja åtkomst till offentliga virtuella IP-adresser (VIP) för ett visst intervall som tillåter åtkomst för RDP. Alla andra fjärranslutna IP-adresser att nekas. Detta fungerar eftersom nätverket ACL: er kan ställas in för en specifik virtuell dator-slutpunkten och åtkomst nekas som standard.

**Exempel – flera regler**

| **Regel #** | **Fjärranslutet undernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Tillåt |
| 200 |159.0.0.0/8 |3389 |Tillåt |

### <a name="rule-order"></a>Regeln ordning
Eftersom flera regler kan anges för en slutpunkt, måste det finnas ett sätt att organisera regler för att avgöra vilka regeln företräde. Regelordningen som anger företräde. Nätverk ACL: er Följ en *lägsta företräde* regel ordning. I exemplet nedan beviljas slutpunkten på port 80 selektivt åtkomst till endast vissa IP-adressintervall. Om du vill konfigurera det här, har vi en neka-regel (regel \# 100) för adresser i området 175.1.0.1/24. En andra regel anges sedan med prioritet 200 som ger åtkomst till alla andra adresser under 175.0.0.0/8.

**Exempel – prioritet för regeln**

| **Regel #** | **Fjärranslutet undernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Neka |
| 200 |175.0.0.0/8 |80 |Tillåt |

## <a name="network-acls-and-load-balanced-sets"></a>Nätverks-ACL: er och läsa in belastningsutjämnade uppsättningar
ACL: er för nätverket kan anges för en belastningsutjämnad uppsättning slutpunkt. Om du anger en ACL för en belastningsutjämnad uppsättning, tillämpas nätverkets ACL på alla virtuella datorer i den belastningsutjämnade uppsättningen. Till exempel om en belastningsutjämnade har skapats med ”Port 80” och den belastningsutjämnade uppsättningen innehåller 3 virtuella datorer skapade nätverket ACL på slutpunkten ”Port 80” på en VM kopplas automatiskt till andra virtuella datorer.

![Nätverks-ACL: er och läsa in belastningsutjämnade uppsättningar](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Nästa steg
[Hantera åtkomstkontrollistor för slutpunkter med hjälp av PowerShell](virtual-networks-acl-powershell.md)

