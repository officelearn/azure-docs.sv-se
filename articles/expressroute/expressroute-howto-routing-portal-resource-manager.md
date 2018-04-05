---
title: 'Konfigurera routning (peering) för en ExpressRoute-krets: hanteraren för filserverresurser: Azure | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: f0f0a31abc4e2d3114d71729c6c447c569295290
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Skapa och ändra peering för en ExpressRoute-krets

Den här artikeln hjälper dig att skapa och hantera routningskonfiguration för en ExpressRoute-krets i Resource Manager-distributionsmodellen med hjälp av Azure portal. Du kan också kontrollera status, uppdatera eller ta bort och ta bort etableringen av peerkopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med kretsen väljer du en artikel från följande lista:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 


## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerad och aktiverade tillstånd att kunna köra cmdlets i nästa avsnitt.
* Om du planerar att använda en delad nyckel/MD5-hash, måste du använda detta på båda sidor av tunneln och begränsa antalet tecken som högst 25.

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänstprovider som erbjuder hanterade Layer 3-tjänster (vanligtvis en IPVPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning för dig. 

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontrollera med leverantören innan du konfigurerar BGP peerkopplingar.
> 
> 

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Läs mer om routning domäner och peerkopplingar [ExpressRoute routningsdomänerna](expressroute-circuit-peerings.md).

## <a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Microsoft peering konfigurationen för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering i ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service prefix annonserade via Microsoft peering, även om filter routning inte har definierats. Microsoft-peering i ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett filter för flödet är kopplat till kretsen. Mer information finns i [konfigurera route filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. Om anslutningsleverantören erbjuder hanteringstjänster Layer 3 kan be du anslutningsleverantören att aktivera Microsoft-peering för dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om anslutningsleverantören inte kan hantera routning av du, när du har skapat din krets fortsätta konfigurationen med nästa steg.

  ![Visa en lista över Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

  * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
  * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
  * **Valfritt -** kunden ASN: Om du är reklam prefix som inte har registrerats peering som tal, kan du ange numret som de har registrerats.
  * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
  * **Valfritt -** en MD5-hash om du väljer att använda en.
3. Du kan välja peering som du vill konfigurera, som visas i följande exempel. Välj Microsofts peering-rad.

  ![Välj Microsoft peering rad](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Konfigurera Microsoft-peering. Följande bild visar ett Konfigurationsexempel på:

  ![Konfigurera Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Spara konfigurationen när du har angett alla parametrar.

  Om kretsen kommer till en-verifiering krävs' tillstånd (som visas i bilden), måste du öppna ett supportärende för att bevisa att ägarskap av prefix till vårt supportteam.

  ![Spara konfigurationen för Microsoft peering](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

  Du kan öppna ett supportärende direkt från portalen, enligt följande exempel:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. När konfigurationen har tagits emot, visas något som liknar följande bild:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Visa Microsoft peering information

Du kan visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Att uppdatera Microsoft peering konfigurationen

Du kan välja raden för peering och ändra peering-egenskaperna.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Ta bort Microsoft-peering

Du kan ta bort peering konfigurationen genom att välja ikonen Ta bort enligt följande bild:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Privat Azure-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azure privat peering konfigurationen för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. Om anslutningsleverantören erbjuder hanteringstjänster Layer 3 kan be du anslutningsleverantören att aktivera privat Azure-peering för dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om anslutningsleverantören inte kan hantera routning av du, när du har skapat din krets fortsätta konfigurationen med nästa steg.

  ![lista](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

  * Ett /30 undernät för den primära länken. Undernätet får inte vara en del av alla adressutrymme som är reserverade för virtuella nätverk.
  * Ett /30 undernät för den sekundära länken. Undernätet får inte vara en del av alla adressutrymme som är reserverade för virtuella nätverk.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
  * **Valfritt -** en MD5-hash om du väljer att använda en.
3. Välj den Azure privat peering raden som visas i följande exempel:

  ![privata](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Konfigurera privat peering. Följande bild visar ett Konfigurationsexempel på:

  ![Konfigurera privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Spara konfigurationen när du har angett alla parametrar. När konfigurationen har tagits emot, visas något som liknar följande exempel:

  ![Spara privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Visa information om Azure privat peering

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

![Visa privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Uppdatera Azure privat peering-konfiguration

Du kan välja raden för peering och ändra peering-egenskaperna.

![Uppdatera privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Ta bort privat Azure-peering

Du kan ta bort peering konfigurationen genom att välja ikonen Ta bort enligt följande bild:

![ta bort privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Offentlig Azure-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azure offentlig peering konfigurationen för en ExpressRoute-krets.

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. Om anslutningsleverantören erbjuder hanteringstjänster Layer 3 kan be du anslutningsleverantören att aktivera offentlig Azure-peering för dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om anslutningsleverantören inte kan hantera routning av du, när du har skapat din krets fortsätta konfigurationen med nästa steg.

  ![Visa en lista med offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurera Azures offentliga peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

  * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * **Valfritt -** en MD5-hash om du väljer att använda en.
3. Välj den Azure offentliga peering raden, enligt följande bild:

  ![Välj offentlig peering rad](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Konfigurera offentlig peering. Följande bild visar ett Konfigurationsexempel på:

  ![Konfigurera offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Spara konfigurationen när du har angett alla parametrar. När konfigurationen har tagits emot, visas något som liknar följande exempel:

  ![Spara offentlig peering konfiguration](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Visa information om Azure offentlig peering

Du kan visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

![Visa offentlig peering egenskaper](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Uppdatera Azure offentlig peering-konfiguration

Du kan välja raden för peering och ändra peering-egenskaperna.

![Välj offentlig peering rad](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Ta bort offentlig Azure-peering

Du kan ta bort peering konfigurationen genom att välja ikonen Ta bort som visas i följande exempel:

![ta bort offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Nästa steg

Nästa steg [länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
