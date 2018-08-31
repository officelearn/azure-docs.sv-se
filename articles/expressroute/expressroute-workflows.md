---
title: Arbetsflöden för att konfigurera en Azure ExpressRoute-krets | Microsoft Docs
description: Den här sidan beskriver hur du arbetsflöden för att konfigurera ExpressRoute-kretsen och peer-kopplingar
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: cherylmc
ms.openlocfilehash: 765050c9c21c7ba752535fc391cc9bb7d8ac4083
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301046"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Arbetsflöden i ExpressRoute för kretsetablering och kretstillstånd
Den här sidan vägleder dig genom tjänsten etablering och routning configuration arbetsflöden på hög nivå.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Följande bild och motsvarande steg visar de uppgifter som du måste följa för att få en ExpressRoute-krets som etablerats slutpunkt till slutpunkt. 

1. Konfigurera en ExpressRoute-krets med hjälp av PowerShell. Följ instruktionerna i den [skapa ExpressRoute-kretsar](expressroute-howto-circuit-classic.md) nedan för mer information.
2. Ordning anslutningen från tjänstleverantören. Den här processen varierar. Kontakta din anslutningsleverantör för mer information om hur du beställer anslutning.
3. Se till att kretsen har etablerats har genom att verifiera ExpressRoute-kretsen Etableringsstatus via PowerShell. 
4. Konfigurera routningsdomäner. Om din anslutningsleverantör hanterar Layer 3 åt dig, kommer de att konfigurera routning för din krets. Om din anslutningsleverantör erbjuder endast Layer 2-tjänster, måste du konfigurera routning per riktlinjer som beskrivs i den [routningskrav](expressroute-routing.md) och [routningskonfiguration](expressroute-howto-routing-classic.md) sidor.
   
   * Aktiverar Azures privata peering – aktivera den här peering för att ansluta till virtuella datorer / molntjänster som distribuerats i virtuella nätverk.

   * Aktivera Microsoft-peering – aktivera det här alternativet för åtkomst till Office 365 och Dynamics 365. Dessutom är alla Azure PaaS-tjänster tillgängliga via Microsoft-peering.
     
     > [!IMPORTANT]
     > Du måste se till att du använder en separat proxyserver / edge för att ansluta till Microsoft än som du använder för Internet. Använder samma kant för både ExpressRoute och Internet orsaka asymmetrisk Routning och orsaka avbrott i anslutningen för ditt nätverk.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Länka virtuella nätverk till ExpressRoute-kretsar – du kan länka virtuella nätverk till ExpressRoute-kretsen. Följ instruktionerna [att länka virtuella nätverk](expressroute-howto-linkvnet-arm.md) för din krets. Dessa virtuella nätverk kan antingen vara i samma Azure-prenumeration som ExpressRoute-krets eller kan vara i en annan prenumeration.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-krets Etableringsstatus
Varje ExpressRoute-krets har två lägen:

* Etableringsstatus för service provider
* Status

Status representerar Microsofts Etableringsstatus. Den här egenskapen är inställd på aktiverad när du skapar en Expressroute-krets

Etableringsstatus för anslutning providern representerar tillstånd i anslutningsleverantörens sida. Det kan antingen vara *NotProvisioned*, *etablering*, eller *etablerad*. ExpressRoute-kretsen måste vara i etablerad tillstånd för att du ska kunna använda den.

### <a name="possible-states-of-an-expressroute-circuit"></a>Möjliga tillstånd för en ExpressRoute-krets
Det här avsnittet innehåller ut möjliga tillstånd för en ExpressRoute-krets.

**Vid skapandet**

ExpressRoute-kretsen i följande tillstånd visas när du kör PowerShell-cmdlet för att skapa ExpressRoute-kretsen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**När anslutningsleverantör är processen för att etablera kretsen**

ExpressRoute-kretsen i följande tillstånd visas när du skickar tjänstnyckeln till anslutningsleverantören och de har startat etableringen.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**När connectivity-leverantör har slutfört etableringsprocessen**

ExpressRoute-kretsen i följande tillstånd visas när anslutningsleverantören har slutfört etableringsprocessen.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Etablerad och aktiverad är endast tillståndet kretsen kan ha att kunna använda den. Om du använder en Layer 2-provider kan konfigurera du routning för din krets endast när den är i det här tillståndet.

**När anslutningsleverantör håller på att avetablera kretsen**

Om du har begärt tjänstleverantör för att ta bort etableringen av ExpressRoute-kretsen visas kretsen inställd på följande tillstånd när tjänstleverantören har slutförts avetableringsprocessen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Du kan välja att aktivera den igen om det behövs, eller kör PowerShell-cmdletar för att ta bort kretsen.  

> [!IMPORTANT]
> Om du kör PowerShell-cmdlet för att ta bort kretsen vid etablering av Korsanslutningens eller etablerad misslyckas åtgärden. Kontakta din anslutningsleverantör att avetablera ExpressRoute-kretsen först och sedan ta bort kretsen. Microsoft fortsätter att debitera kretsen tills du kör PowerShell-cmdlet för att ta bort kretsen.
> 
> 

## <a name="routing-session-configuration-state"></a>Routning sessionstillstånd för konfiguration
BGP Etableringsstatus får du reda på om BGP-sessionen har aktiverats på Microsoft edge. Tillståndet måste aktiveras att kunna använda peer-kopplingen.

Det är viktigt att kontrollera tillståndet för BGP-sessionen särskilt för Microsoft-peering. Förutom BGP Etableringsstatus, det finns ett annat tillstånd som kallas *annonserade offentliga prefix tillstånd*. Tillståndet annonserade offentliga prefix måste vara i *konfigurerats* status, både för BGP-sessionen att vara igång och för din routning för att fungera slutpunkt till slutpunkt. 

Om tillståndet annonserade offentliga prefix har angetts till en *verifiering krävs* tillstånd, BGP-sessionen inte är aktiverad, eftersom de annonserade prefix inte matchade det AS-nummer i någon av routningsregister. 

> [!IMPORTANT]
> Om tillståndet annonserade offentliga prefix är i *manuell verifiering* tillstånd, måste du öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och bevisa att du äger IP-adresser annonseras tillsammans med den associerade autonomt systemnummer.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

