---
title: 'Azure ExpressRoute: Konfigurera peering'
description: I den här artikeln beskrivs stegen för att skapa och etablera ExpressRoute private och Microsoft peering. Den här artikeln visar också hur du kontrollerar status, uppdatering eller ta bort peerings för en krets.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264796"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Skapa och ändra peering för en ExpressRoute-krets

Den här artikeln hjälper dig att skapa och hantera routningskonfiguration för en Azure Resource Manager (ARM) ExpressRoute-krets med hjälp av Azure-portalen. Du kan också kontrollera status, uppdatera eller ta bort och avetablera peerings för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med kretsen väljer du en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Offentlig peering](about-public-peering.md)
> * [Video - Privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Du kan konfigurera privat peering och Microsoft-peering för en ExpressRoute-krets (Offentlig Azure-peering är föråldrad för nya kretsar). Peerings kan konfigureras i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Mer information om routning av domäner och peerings finns i [ExpressRoute routningsdomäner](expressroute-circuit-peerings.md). Information om offentlig peering finns i [ExpressRoute offentlig peering](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. För att konfigurera peering(s) måste ExpressRoute-kretsen vara i ett etablerat och aktiverat tillstånd. 
* Om du planerar att använda en delad nyckel/MD5-hash måste du använda den på båda sidor av tunneln och begränsa antalet alfanumeriska tecken till högst 25. Specialtecken stöds inte. 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänsteleverantör som erbjuder hanterade Layer 3-tjänster (vanligtvis ett IPVPN, till exempel MPLS), konfigurerar och hanterar anslutningsleverantören routning åt dig. 

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontrollera med din tjänsteleverantör innan du konfigurerar BGP-peerings.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Microsoft-peering-konfigurationen för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla tjänstprefix annonserade via Microsoft-peering, även om vägfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade den 1 augusti 2017 eller senare kommer inte att ha några prefix som annonseras förrän ett flödesfilter är kopplat till kretsen. Mer information finns i [Konfigurera ett flödesfilter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Kontrollera **providerns status** för att säkerställa att kretsen är helt etablerad av anslutningsleverantören innan du fortsätter ytterligare.

   Om din anslutningsleverantör erbjuder hanterade Layer 3-tjänster kan du be anslutningsleverantören att aktivera Microsoft-peering åt dig. I så fall behöver du inte följa instruktionerna i nästa avsnitt. Men om din anslutningsleverantör inte hanterar routning åt dig, när du har skapat din krets, fortsätt med dessa steg.

   **Krets - Providerstatus: Ej etablerad**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Krets - Providerstatus: Etablerad**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från detta undernät kommer du att tilldela den första användbara IP-adressen till din router som Microsoft använder den andra användbara IP för sin router.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från detta undernät kommer du att tilldela den första användbara IP-adressen till din router som Microsoft använder den andra användbara IP för sin router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * **Valfritt -** Kundens ASN: Om du annonserar prefix som inte är registrerade till peering AS-numret kan du ange det AS-nummer som de är registrerade till.
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * **Valfritt -** En MD5-hash om du väljer att använda en.
3. Du kan välja den peering du vill konfigurera, som visas i följande exempel. Välj Microsofts peering-rad.

   [![Markera Microsoft-peering-raden](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Markera Microsoft-peering-raden")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurera Microsoft-peering. **Spara** konfigurationen när du har angett alla parametrar. Följande bild visar en exempelkonfiguration:

   ![Konfigurera Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft verifierar om de angivna prefixen för annonserade offentliga prefix och peer ASN (eller "Kundens ASN") har tilldelats dig i Internet-routningsregistret. Om du får de offentliga prefixen från en annan entitet och om tilldelningen inte registreras med routningsregistret slutförs inte den automatiska valideringen och kräver manuell validering. Om den automatiska valideringen misslyckas visas meddelandet "Validering behövs". 
>
> Om meddelandet "Validering behövs" samlar du in de dokument som visar att de offentliga prefixen tilldelas din organisation av den entitet som anges som ägare till prefixen i routningsregistret och skickar dessa dokument för manuell validering av öppna en supportbiljett som visas nedan. 
>

   Om din krets kommer till ett "Valideringsbestådes"-tillstånd måste du öppna en supportbiljett för att visa bevis på äganderätten till prefixen till vårt supportteam. Du kan öppna en supportbiljett direkt från portalen, som visas i följande exempel:

   ![Validering behövs - supportbiljett](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. När konfigurationen har accepterats visas något som liknar följande avbildning:

   ![Peering-status: Konfigurerad](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Peering-status: Konfigurerad")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Så här visar du Microsofts peering-information

Du kan visa egenskaperna för Microsoft-peering genom att välja raden för peering.

[![Visa egenskaper för Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Visa egenskaper")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan markera raden för den peering som du vill ändra, sedan ändra peering-egenskaperna och spara ändringarna.

![Välj peering-rad](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Så här tar du bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att klicka på borttagningsikonen, som visas i följande bild:

![Ta bort peering](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Azures privata peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azure-konfigurationen för privat peering för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. 

   Om din anslutningsleverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutningsleverantör att aktivera Azure-privat peering åt dig. I så fall behöver du inte följa instruktionerna i nästa avsnitt. Men om din anslutningsleverantör inte hanterar routning åt dig, när du har skapat din krets, fortsätt med nästa steg.

   **Krets - Providerstatus: Ej etablerad**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Krets - Providerstatus: Etablerad**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett /30 undernät för den primära länken. Undernätet får inte ingå i något adressutrymme som är reserverat för virtuella nätverk. Från detta undernät kommer du att tilldela den första användbara IP-adressen till din router som Microsoft använder den andra användbara IP för sin router.
   * Ett /30 undernät för den sekundära länken. Undernätet får inte ingå i något adressutrymme som är reserverat för virtuella nätverk. Från detta undernät kommer du att tilldela den första användbara IP-adressen till din router som Microsoft använder den andra användbara IP för sin router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-nummer för den här peeringen förutom antalet från 65515 till 65520, inklusive.
   * Du måste annonsera vägarna från din lokala Edge-router till Azure via BGP när du konfigurerar den privata peering.
   * **Valfritt -** En MD5-hash om du väljer att använda en.
3. Välj azure-raden för privat peering, som visas i följande exempel:

   [![Markera den privata peering-raden](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Markera den privata peering-raden")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurera privat peering. **Spara** konfigurationen när du har angett alla parametrar.

   ![konfigurera privat peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. När konfigurationen har accepterats visas något som liknar följande exempel:

   ![sparad privat peering](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Så här visar du Azures privata peering-information

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

[![Visa privata peering-egenskaper](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Visa privata peering-egenskaper")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Uppdatera konfigurationen av Azures privata peering

Du kan välja raden för peering och ändra peering-egenskaperna. Spara ändringarna efter uppdateringen.

![uppdatera privat peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Så här tar du bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att välja borttagningsikonen, som visas i följande bild:

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
