---
title: 'Azure-ExpressRoute: Konfigurera peering'
description: I den här artikeln dokumenteras stegen för att skapa och tillhandahålla ExpressRoute privat och Microsoft-peering. Den här artikeln visar också hur du kontrollerar status, uppdaterar eller tar bort peer-kopplingar för en krets.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: duau
ms.openlocfilehash: ccbd9645ac7d331c06e528298b3a45a184c6cc49
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652235"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Skapa och ändra peering för en ExpressRoute-krets

Den här artikeln hjälper dig att skapa och hantera konfiguration av routning för en Azure Resource Manager (ARM) ExpressRoute-krets med hjälp av Azure Portal. Du kan också kontrol lera status, uppdatera eller ta bort och avetablera peer-kopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Offentlig peering](about-public-peering.md)
> * [Video-privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Du kan konfigurera privat peering och Microsoft-peering för en ExpressRoute-krets (offentlig Azure-peering är inaktuell för nya kretsar). Peering kan konfigureras i vilken ordning du väljer. Dock måste du se till att du slutför konfigurationen av en peering i taget. Mer information om routningsdomäner och peering-domäner finns i [ExpressRoute](expressroute-circuit-peerings.md). Information om offentlig peering finns i [ExpressRoute offentlig peering](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. För att kunna konfigurera peering (-ar) måste ExpressRoute-kretsen vara i ett tillstånd med etablerad och aktiverat. 
* Om du planerar att använda en delad nyckel/MD5-hash, måste du använda detta på båda sidor av tunneln och begränsa antalet alfanumeriska tecken till högst 25. Specialtecken stöds inte. 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis en IPVPN, t. ex. MPLS), konfigurerar och hanterar din anslutnings leverantör routning åt dig. 

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontakta tjänst leverantören innan du konfigurerar BGP-peering.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Microsofts peering-konfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänst prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen. Mer information finns i [Konfigurera ett flödes filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Kontrol lera **providerns status** för att säkerställa att kretsen är helt etablerad av anslutnings leverantören innan du fortsätter.

   Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Microsoft-peering åt dig. I så fall behöver du inte följa instruktionerna som anges i nästa avsnitt. Men om din anslutnings leverantör inte hanterar routning åt dig, kan du fortsätta med de här stegen när du har skapat din krets.

   **Krets-Provider-status: inte etablerad**

   [![Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som visar providerns status inställd på "inte etablerad".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Krets-Provider-status: etablerad**

   [![Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som markerar providerns status som "etablerad".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här under nätet tilldelar du den första användbara IP-adressen till din router eftersom Microsoft använder den andra användbara IP-adressen för sin router.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här under nätet tilldelar du den första användbara IP-adressen till din router eftersom Microsoft använder den andra användbara IP-adressen för sin router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * **Valfritt-** Kund-ASN: om du annonserar prefix som inte har registrerats för peering som ett nummer kan du ange det AS-nummer som de är registrerade för.
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * **Valfritt-** En MD5-hash om du väljer att använda en.
3. Du kan välja den peering som du vill konfigurera, som du ser i följande exempel. Välj Microsofts peering-rad.

   [![Välj Microsoft-peering-raden](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Välj Microsoft-peering-raden")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurera Microsoft-peering. **Spara** konfigurationen när du har angett alla parametrar. Följande bild visar en exempel konfiguration:

   ![Konfigurera Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas visas meddelandet "validering krävs". 
>
> Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering genom att öppna ett support ärende som visas nedan. 
>

   Om din krets har statusen "validering krävs" måste du öppna ett support ärende för att Visa bevis för ägarskapet av prefixen till vårt support team. Du kan öppna ett support ärende direkt från portalen, som du ser i följande exempel:

   ![Validering krävs – support ärende](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. När konfigurationen har godkänts ser du något som liknar följande bild:

   ![Peering-status: konfigurerad](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Peering-status: konfigurerad")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Så här visar du Microsofts peering-information

Du kan visa egenskaperna för Microsoft-peering genom att välja raden för peer koppling.

[![Visa egenskaper för Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Visa egenskaper")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan välja raden för peering som du vill ändra och sedan ändra peering-egenskaperna och spara dina ändringar.

![Välj peering-rad](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Så här tar du bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att klicka på ikonen Ta bort, som du ser i följande bild:

![Ta bort peer koppling](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Privat peering i Azure

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Azures konfiguration för privata peering för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. 

   Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Azures privata peering åt dig. I så fall behöver du inte följa instruktionerna som anges i nästa avsnitt. Men om din anslutnings leverantör inte hanterar routning åt dig, kan du fortsätta med nästa steg när du har skapat din krets.

   **Krets-Provider-status: inte etablerad**

   [![Skärm bild som visar översikts sidan för ExpressRoute demo-kretsen med en röd ruta som markerar leverantörens status som är inställt på "inte etablerad".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Krets-Provider-status: etablerad**

   [![Skärm bild som visar översikts sidan för ExpressRoute-demo kretsen med en röd ruta som markerar leverantörens status som är inställd på "etablerad".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett /30 undernät för den primära länken. Under nätet får inte ingå i något adress utrymme som är reserverat för virtuella nätverk. Från det här under nätet tilldelar du den första användbara IP-adressen till din router eftersom Microsoft använder den andra användbara IP-adressen för sin router.
   * Ett /30 undernät för den sekundära länken. Under nätet får inte ingå i något adress utrymme som är reserverat för virtuella nätverk. Från det här under nätet tilldelar du den första användbara IP-adressen till din router eftersom Microsoft använder den andra användbara IP-adressen för sin router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-nummer för denna peering förutom numret från 65515 till 65520.
   * Du måste annonsera vägarna från din lokala Edge-router till Azure via BGP när du konfigurerar den privata peeringen.
   * **Valfritt-** En MD5-hash om du väljer att använda en.
3. Välj den Azure-privata peering-raden som visas i följande exempel:

   [![Välj den privata peering-raden](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Välj den privata peering-raden")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurera privat peering. **Spara** konfigurationen när du har angett alla parametrar.

   ![Konfigurera privat peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. När konfigurationen har godkänts ser du något som liknar följande exempel:

   ![Sparad privat peering](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Så här visar du Azures privata peering-information

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

[![Visa egenskaper för privata peering](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Visa egenskaper för privata peering")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Uppdatera konfigurationen av Azures privata peering

Du kan välja raden för peering och ändra peering-egenskaperna. Spara ändringarna efter uppdateringen.

![uppdatera privat peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Så här tar du bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort, som du ser i följande bild:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global Reach-anslutningar tas bort innan du kör det här exemplet. 
> 
> 

![ta bort privat peering](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Nästa steg

Nästa steg, [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
