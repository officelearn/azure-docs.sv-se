---
title: 'Konfigurera peering för en krets - ExpressRoute: Azure | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6d1ce56ef66d224b89f49a00c2883ebbf22a5745
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60365068"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Skapa och ändra peering för en ExpressRoute-krets

Den här artikeln hjälper dig att skapa och hantera routningskonfiguration för en ExpressRoute-krets i Resource Manager-distributionsmodellen med Azure portal. Du kan också kontrollera status, uppdatera eller ta bort och Avetablerar peerings för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Läs mer om routning domäner och peerings [om kretsar och peerings](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd att kunna köra cmdletarna i nästa avsnitt.
* Om du planerar att använda en delad nyckel/MD5-hash, måste du använda det på båda sidorna av tunneln och begränsa antalet alfanumeriska tecken till högst 25. Specialtecken stöds inte. 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en leverantör av tjänster som erbjuder hanterade Layer 3-tjänster (vanligtvis en IPVPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig. 

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontrollera med din tjänstleverantör innan du konfigurerar BGP-peerings.
> 
> 

## <a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Microsofts peeringskonfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service-prefix som annonseras via Microsoft-peering, även om flödesfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett flödesfilter är kopplad till kretsen. Mer information finns i [konfigurera ett flödesfilter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be din anslutningsleverantör för att Microsoft peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutningsleverantör inte kan hantera routning åt dig, när du har skapat kretsen, fortsätta konfigurationen med nästa steg.

   ![lista över Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * **Valfritt –** kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS number, kan du ange det AS-nummer som de är registrerade.
   * Routningens registernamn: Du kan ange RIR / IR mot vilken AS-numret och prefixet är registrerade.
   * **Valfritt –** en MD5-hash om du väljer att använda en.
3. Du kan välja den peering som du vill konfigurera, som visas i följande exempel. Välj Microsofts peering-rad.

   ![Välj Microsofts peering-rad](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Konfigurera Microsoft-peering. Följande bild visar ett Konfigurationsexempel:

   ![Konfigurera Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Spara konfigurationen när du har angett alla parametrar.

   Om din krets kommer till en ”verifiering krävs” tillstånd (som visas i bild), måste du öppna ett supportärende för att bevisa ägarskapet för prefixen till supportteamet.

   ![Spara konfigurationen för Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   Du kan öppna ett supportärende direkt från portalen, enligt följande exempel:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. När konfigurationen har tagits emot, visas något som liknar följande bild:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Så här visar du Microsofts peering-information

Du kan visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Att uppdatera konfigurationen för Microsoft-peering

Du kan välja raden för peering och ändra peering-egenskaperna.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Ta bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort enligt följande bild:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Azures privata peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azures privata peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutningsleverantör inte kan hantera routning åt dig, när du har skapat kretsen, fortsätta konfigurationen med nästa steg.

   ![lista](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett /30 undernät för den primära länken. Undernätet får inte vara en del av något adressutrymme som reserverats för virtuella nätverk. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett /30 undernät för den sekundära länken. Undernätet får inte vara en del av något adressutrymme som reserverats för virtuella nätverk. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen förutom tal från 65515 till 65520, portintervallet.
   * **Valfritt –** en MD5-hash om du väljer att använda en.
3. Välj den Azure-privata peering-raden som visas i följande exempel:

   ![privat](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Konfigurera privat peering. Följande bild visar ett Konfigurationsexempel:

   ![Konfigurera privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Spara konfigurationen när du har angett alla parametrar. När konfigurationen har tagits emot, visas något som liknar följande exempel:

   ![Spara privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Så här visar du Azures privata peering-information

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

![Visa privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Att uppdatera konfigurationen av Azures privata peering

Du kan välja raden för peering och ändra peering-egenskaperna.

![Uppdatera privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Att ta bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort enligt följande bild:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global räckvidd anslutningar tas bort innan du kör det här exemplet. 
> 
> 

![ta bort privat peering](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Azures offentliga peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be anslutningsleverantören aktiverar Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutningsleverantör inte kan hantera routning åt dig, när du har skapat kretsen, fortsätta konfigurationen med nästa steg.

   ![lista över offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurera Azures offentliga peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router. 
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * **Valfritt –** en MD5-hash om du väljer att använda en.
3. Välj den Azure offentliga peering-raden som visas i följande bild:

   ![Välj offentliga peering-raden](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Konfigurera offentlig peering. Följande bild visar ett Konfigurationsexempel:

   ![Konfigurera offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Spara konfigurationen när du har angett alla parametrar. När konfigurationen har tagits emot, visas något som liknar följande exempel:

   ![Spara offentlig peering-konfigurationen](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Så här visar du Azures offentliga peering-information

Du kan visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

![Visa egenskaper för offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Att uppdatera konfigurationen av Azures offentliga peering

Du kan välja raden för peering och ändra peering-egenskaperna.

![Välj offentliga peering-raden](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Att ta bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort som visas i följande exempel:

![ta bort offentlig peering](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Nästa steg

Nästa steg [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
