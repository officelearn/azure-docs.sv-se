---
title: "Arbetsflöden för att konfigurera en ExpressRoute-krets | Microsoft Docs"
description: "Den här sidan vägleder dig genom arbetsflödena för att konfigurera ExpressRoute-krets och peerkopplingar"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Arbetsflöden i ExpressRoute för kretsetablering och kretstillstånd
Den här sidan vägleder dig genom tjänsten etablering och routning configuration arbetsflöden på en hög nivå.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Följande bild och motsvarande steg visar de uppgifter som du måste följa för att få en ExpressRoute-krets etablerats slutpunkt till slutpunkt. 

1. Använd PowerShell för att konfigurera en ExpressRoute-krets. Följ instruktionerna i den [skapa ExpressRoute-kretsar](expressroute-howto-circuit-classic.md) mer information.
2. Ordning anslutningar från Internet-leverantören. Den här processen varierar. Kontakta anslutningsleverantören för mer information om hur du beställer anslutningen.
3. Kontrollera att kretsen har har etablerats genom att verifiera ExpressRoute-kretsen Etableringsstatus via PowerShell. 
4. Konfigurera routning domäner. Om anslutningsleverantören hanterar Layer 3 åt dig, kommer de att konfigurera routning för kretsen. Om anslutningsleverantören erbjuder endast nivå 2-tjänster, måste du konfigurera routning per riktlinjer som beskrivs i den [routningskrav](expressroute-routing.md) och [routningskonfiguration](expressroute-howto-routing-classic.md) sidor.
   
   * Aktivera privat Azure-peering - du måste aktivera den här peering för att ansluta till virtuella datorer / molntjänster distribueras inom virtuella nätverk.
   * Aktivera Azure offentlig peering - måste du aktivera Azure offentlig peering om du vill ansluta till Azure-tjänster finns på den offentliga IP-adresser. Detta är ett krav för att komma åt Azure-resurser om du har valt att aktivera standardroutning för privat Azure-peering.
   * Aktivera Microsoft-peering - måste du aktivera åtkomst till Office 365 och Dynamics 365. 
     
     > [!IMPORTANT]
     > Du måste se till att du använder en separat proxyserver / kant för att ansluta till Microsoft än som du använder för Internet. Med hjälp av samma kant för ExpressRoute- och Internet orsaka asymmetriska Routning och orsaka anslutningen avbrott för ditt nätverk.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Länka virtuella nätverk för ExpressRoute-kretsar - du kan länka virtuella nätverk till ExpressRoute-kretsen. Följ anvisningarna [att länka Vnet](expressroute-howto-linkvnet-arm.md) till kretsen. Dessa Vnet kan antingen vara i samma Azure-prenumerationen som ExpressRoute-kretsen eller kan vara i en annan prenumeration.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-krets etablering tillstånd
Varje ExpressRoute-kretsen har två lägen:

* Etableringsstatusen för service provider
* Status

Status representerar Microsofts etablering. Ange den här egenskapen är aktiverad när du skapar en Expressroute-krets

Anslutningen providern etableringsstatusen representerar tillståndet på anslutningen leverantörens sida. Det kan antingen vara *NotProvisioned*, *etablering*, eller *etablerad*. ExpressRoute-kretsen måste vara i etablerad tillstånd att kunna använda den.

### <a name="possible-states-of-an-expressroute-circuit"></a>Möjliga tillstånd för en ExpressRoute-krets
Det här avsnittet innehåller ut möjliga tillstånd för en ExpressRoute-krets.

**Vid skapandet**

ExpressRoute-kretsen i följande läge visas när du kör PowerShell-cmdlet för att skapa ExpressRoute-kretsen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**När anslutningen providern håller på att etablera kretsen**

ExpressRoute-kretsen i följande läge visas när du skickar tjänstnyckeln till providern för anslutningen och de har startat etableringsprocessen.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**När anslutningen providern har slutfört etableringsprocessen**

ExpressRoute-kretsen i följande läge visas så snart anslutningen providern har slutfört etableringsprocessen.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Etablerad och aktiverad är bara Tillståndet kretsen kan vara i att kunna använda den. Om du använder en nivå 2-provider kan du konfigurera routning för kretsen endast när det är i det här tillståndet.

**När anslutningen provider avetablering kretsen**

Om du har begärt leverantören att ta bort etableringen av ExpressRoute-kretsen visas kretsen angetts till följande tillstånd när tjänstleverantör har slutförts avetableringsprocessen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Du kan välja att aktivera det igen om det behövs eller som kör PowerShell-cmdletar för att ta bort kretsen.  

> [!IMPORTANT]
> Om du kör PowerShell-cmdlet för att ta bort kretsen vid etablering av ServiceProviderProvisioningState eller etablerad misslyckas åtgärden. Se tillsammans med anslutningsleverantören för att ta bort etableringen ExpressRoute-kretsen först och sedan ta bort kretsen. Microsoft fortsätter att debiterar kretsen förrän du kör PowerShell-cmdlet för att ta bort kretsen.
> 
> 

## <a name="routing-session-configuration-state"></a>Routning sessionstillstånd konfiguration
BGP Etableringsstatus får du reda på om BGP-sessionen har aktiverats på Microsoft edge. Tillståndet måste aktiveras för att du ska kunna använda peering.

Det är viktigt att kontrollera tillståndet för BGP-sessionen särskilt för Microsoft-peering. Förutom BGP Etableringsstatus, det finns ett annat tillstånd som kallas *annonserade offentliga prefix tillstånd*. Tillståndet annonserade offentliga prefix måste vara i *konfigurerats* tillstånd, både för BGP-sessionen ska dig och dina routning för att fungera slutpunkt till slutpunkt. 

Om tillståndet annonserade offentliga prefix anges till en *validering krävs* tillstånd, BGP-sessionen inte är aktiverad, som annonserade prefix inte matchade antalet AS i någon av Routning register. 

> [!IMPORTANT]
> Om den annonserade offentliga prefix är i tillståndet *manuell verifiering* tillstånd, måste du öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och bevisa att du äger annonserade längs med associerade autonomt systemnummer IP-adresser.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

