---
title: Vad är en Azure-nätverk för åtkomstkontrollista?
description: Lär dig mer om åtkomstkontrollistor i Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 3a7155380a51273d376226c6be7a004f386181ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035267"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Vad är en slutpunkt för åtkomstkontrollista?

> [!IMPORTANT]
> Azure har två olika [distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-distributionsmodellen. 

En slutpunkt åtkomstkontrollista (ACL) är en säkerhetsförbättring som är tillgängliga för din Azure-distribution. En ACL ger möjlighet att tillåta eller neka trafik för en virtuell dator-slutpunkten. Det här paketet filtrering funktionen ger ett extra säkerhetslager. Du kan ange nätverk åtkomstkontrollistor för slutpunkter endast. Du kan inte ange en ACL för ett virtuellt nätverk eller ett specifikt undernät i ett virtuellt nätverk. Det rekommenderas att använda nätverkssäkerhetsgrupper (NSG) i stället för ACL: er, när det är möjligt. När du använder NSG: er, ersätts åtkomstkontrollistan för slutpunkten och inte längre tillämpas. Mer information om Nätverkssäkerhetsgrupper finns [översikt över Network security group](security-overview.md)

ACL: er kan konfigureras med hjälp av PowerShell eller Azure-portalen. För att konfigurera ett nätverk ACL med hjälp av PowerShell, se [hantera åtkomstkontrollistor för slutpunkter med hjälp av PowerShell](virtual-networks-acl-powershell.md). För att konfigurera ett nätverk ACL med hjälp av Azure portal, se [så här ställer du in slutpunkter till en virtuell dator](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Med Network ACL: er kan göra du följande:

* Tillåta eller neka inkommande trafik baserat på fjärrundernätet IPv4-adressintervall till en slutpunkt för kanalindata på en virtuell dator.
* Svartlistat IP-adresser
* Skapa flera regler per virtuell datorslutpunkt
* Använd regel ordning för att säkerställa att rätt uppsättning regler som används på en viss virtuell dator-slutpunkt (lägsta till högsta)
* Ange en ACL för en specifik fjärrundernätet IPv4-adress.

Se den [Azure-begränsningar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) artikeln ACL gränser för.

## <a name="how-acls-work"></a>Hur åtkomstkontrollposter fungerar
En ACL är ett objekt som innehåller en lista över regler. När du skapar en ACL och tillämpa den på en virtuell dator-slutpunkt, sker filtrering av nätverkspaket på värdnoden för den virtuella datorn. Det innebär att trafik från IP-adresser filtreras efter värdnoden för matchande ACL-regler i stället för på den virtuella datorn. Detta förhindrar att den virtuella datorn utgifter dyrbar CPU-cykler på filtrering av nätverkspaket.

När en virtuell dator skapas, placera en standard-ACL på plats för att blockera all inkommande trafik. Men om en slutpunkt har skapats för (port 3389), sedan standard ACL ändras för att tillåta all inkommande trafik för denna slutpunkt. Inkommande trafik från alla fjärranslutet undernät tillåts sedan till denna slutpunkt och ingen brandvägg etablering krävs. Alla andra portar blockeras för inkommande trafik, såvida inte slutpunkter har skapats för dessa portar. Utgående trafik tillåts som standard.

**Exempeltabell för standard-ACL**

| **Regeln #** | **Fjärrundernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Tillåta |

## <a name="permit-and-deny"></a>Tillåta och neka
Du kan selektivt tillåta eller neka nätverkstrafik för en slutpunkt för indata på en virtuell dator genom att skapa regler som anger ”Tillåt” eller ”neka”. Det är viktigt att notera att som standard när en slutpunkt skapas alla tillåts trafik till slutpunkten. Därför är det viktigt att förstå hur du skapar tillstånd/neka regler och placera dem i rätt ordning prioritet om du vill ha detaljerad kontroll över den nätverkstrafik som du väljer att tillåta för att nå slutpunkten för virtuell dator.

Saker att tänka på:

1. **Inga ACL –** som standard när en slutpunkt som vi tillåta alla för slutpunkten.
2. **Tillåta -** när du lägger till en eller flera ”Tillåt” intervall du neka alla intervall som standard. Endast paket från det tillåtna IP-adressintervallet kommer att kunna kommunicera med virtuella datorslutpunkt.
3. **Neka -** när du lägger till en eller flera ”neka” intervall, tillåter du alla andra intervall för trafik som standard.
4. **Kombinationen av Tillåt och neka -** du kan använda en kombination av ”Tillåt” och ”neka” när du vill få en specifik IP-adressintervall som ska beviljas eller nekas.

## <a name="rules-and-rule-precedence"></a>Regler och regelprioritet
Nätverks-ACL: er kan ställas in på den specifika virtuella datorers slutpunkter. Du kan till exempel ange ett nätverk ACL för en RDP-slutpunkt som skapats på en virtuell dator som Lås åtkomst för vissa IP-adresser. Tabellen nedan visar ett sätt att bevilja åtkomst till offentliga virtuella IP-adresser (VIP) för ett visst intervall för att tillåta åtkomst för RDP. Alla andra fjärranslutna IP-adresser nekas. Vi följer en *lägsta företräde* regel ordning.

### <a name="multiple-rules"></a>Flera regler
I exemplet nedan om du vill tillåta åtkomst till RDP-slutpunkt från två offentliga IPv4-adressintervall (65.0.0.0/8 och 159.0.0.0/8), du kan åstadkomma detta genom att ange två *tillåta* regler. I det här fallet eftersom RDP skapas som standard för en virtuell dator, kan du låsa åtkomsten till RDP-porten baserat på ett fjärranslutet undernät. Exemplet nedan visar ett sätt att bevilja åtkomst till offentliga virtuella IP-adresser (VIP) för ett visst intervall för att tillåta åtkomst för RDP. Alla andra fjärranslutna IP-adresser nekas. Detta fungerar eftersom åtkomstkontrollistor i nätverket kan ställas in för en specifik virtuell dator-slutpunkten och åtkomsten nekas som standard.

**Exempel – flera regler**

| **Regeln #** | **Fjärrundernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Tillåta |
| 200 |159.0.0.0/8 |3389 |Tillåta |

### <a name="rule-order"></a>Regel för turordning
Eftersom du kan ange flera regler för en slutpunkt, måste det finnas ett sätt att ordna regler för att avgöra vilka regeln företräde. Regel för turordning anger företräde. Nätverks-ACL: er följer en *lägsta företräde* regel ordning. I exemplet nedan beviljas slutpunkten på port 80 selektivt åtkomst till endast vissa IP-adressintervall. Om du vill konfigurera detta, vi har en neka-regeln (regel \# 100) för adresser i området 175.1.0.1/24. En andra regel anges sedan med prioritet 200 som tillåter åtkomst till alla adresser under 175.0.0.0/8.

**Exempel – regelprioritet**

| **Regeln #** | **Fjärrundernät** | **Slutpunkt** | **Tillåt/neka** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Neka |
| 200 |175.0.0.0/8 |80 |Tillåta |

## <a name="network-acls-and-load-balanced-sets"></a>Nätverks-ACL: er och belastningsutjämnade uppsättningar
Nätverks-ACL: er kan anges på en belastningsutjämnad uppsättning slutpunkt. Om en ACL har angetts för en belastningsutjämnad uppsättning, används network ACL på alla virtuella datorer i den belastningsutjämnade uppsättningen. Till exempel om en belastningsutjämnad uppsättning skapas med ”Port 80” och den belastningsutjämnade uppsättningen innehåller 3 virtuella datorer, nätverket ACL som skapats på slutpunkten ”Port 80” för en virtuell dator kopplas automatiskt till andra virtuella datorer.

![Nätverks-ACL: er och belastningsutjämnade uppsättningar](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Nästa steg
[Hantera åtkomstkontrollistor för slutpunkter med hjälp av PowerShell](virtual-networks-acl-powershell.md)

