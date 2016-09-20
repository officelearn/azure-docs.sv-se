<properties
   pageTitle="Så här konfigurerar du routning för en ExpressRoute-krets med Azure Portal | Microsoft Azure"
   description="Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc"/>

# Skapa och ändra routning för en ExpressRoute-krets



> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)
[PowerShell - klassiskt](expressroute-howto-routing-classic.md)



Den här artikeln vägleder dig igenom stegen för att skapa och hantera routningskonfiguration för en ExpressRoute-krets med hjälp av Azure-portalen och distributionsmodellen för Azure Resource Manager.

**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Förutsättningar för konfiguration

- Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
- Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd för att du ska kunna köra cmdletarna som beskrivs nedan.

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en Internet-leverantör som erbjuder hanteringstjänster till Layer 3 (vanligtvis en IPVPN, t.ex. MPLS) kommer anslutningsleverantören konfigurera och hantera routning för dig. 


>[AZURE.IMPORTANT] Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontakta din tjänstleverantör innan du konfigurerar BGP-peerings.

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. 

## Azures privata peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures privata peeringskonfiguration för en ExpressRoute-krets. 

### Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

    - Ett /30 undernät för den primära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
    - Ett /30 undernät för den sekundära länken. Detta får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
    - Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
    - AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
    - En MD5-hash om du väljer att använda en. **Det här är valfritt**.


3. Välj den Azure-privata peering-raden som visas nedan.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
    

4. Konfigurera privat peering. Bilden nedan visar ett konfigurationsexempel.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

    
5. Spara konfigurationen när du har angett alla parametrar. När konfigurationen har tagits emot, visas något som liknar exemplet nedan.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)
    

### Så här visar du Azures privata peering-information

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)


### Uppdatera konfigurationen av Azures privata peering

Du kan välja raden för peering och ändra peering-egenskaperna. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### Så här tar du bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort som visas nedan.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)


## Azures offentliga peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets. 

### Så här skapar du Azures offentliga peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Konfigurera Azures offentliga peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

    - Ett /30 undernät för den primära länken. 
    - Ett /30 undernät för den sekundära länken. 
    - Alla IP-adresser som används för att konfigurera den här peeringen måste vara giltig offentliga IPv4-adresser.
    - Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
    - AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
    - En MD5-hash om du väljer att använda en. **Det här är valfritt**.

3. Välj den Azure-offentliga peering-raden som visas nedan.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
    

4. Konfigurera offentlig peering. Bilden nedan visar ett konfigurationsexempel.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

    
5. Spara konfigurationen när du har angett alla parametrar. När konfigurationen har tagits emot, visas något som liknar exemplet nedan.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)
    

### Så här visar du Azures offentliga peering-information

Du kan visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)


### Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan välja raden för peering och ändra peering-egenskaperna. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort som visas nedan.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)


## Microsoft-peering

Det här avsnittet innehåller anvisningar om hur du skapar, hämtar, uppdaterar och tar bort Microsofts peeringskonfiguration för en ExpressRoute-krets. 

### Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

    - Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
    - Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
    - Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
    - AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
    - **Annonserade prefix:** Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Du kan skicka en kommaavgränsad lista om du planerar att skicka en uppsättning prefix. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
    - **Kund-ASN:** Om du har reklamprefix som inte är registrerade på peeringens AS-nummer, kan du ange det AS-nummer som de är registrerade på. **Det här är valfritt**.
    - **Routningens registernamn:** Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade. **Det här är valfritt.**
    - En MD5-hash om du väljer att använda en. **Det här är valfritt.**
    
3. Du kan välja den peering som du vill konfigurera enligt nedan. Välj Microsofts peering-rad.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
    

4.  Konfigurera Microsoft-peering. Bilden nedan visar ett konfigurationsexempel.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)

    
5. Spara konfigurationen när du har angett alla parametrar. 

    Om din krets behöver valideras (enligt nedan) måste du öppna en supportbiljett för att bevisa ägarskapet för prefixen till supportteamet.  
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)


    Du kan öppna en supportbiljett direkt från portalen enligt nedan   
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


6. När konfigurationen har tagits emot, visas något som liknar exemplet nedan.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)
    

### Så här visar du Microsofts peering-information

Du kan visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)


### Så här uppdaterar du Microsofts peering-konfiguration

Du kan välja raden för peering och ändra peering-egenskaperna. 


![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)


### Så här tar du bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort som visas nedan.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)



## Nästa steg

Nästa steg [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

-  Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).

-  Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).

-  Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).




<!--HONumber=sep16_HO1-->


