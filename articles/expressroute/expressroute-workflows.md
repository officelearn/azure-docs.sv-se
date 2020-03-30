---
title: 'Azure ExpressRoute: Arbetsflöden för kretskonfiguration'
description: På den här sidan visas arbetsflöden för att konfigurera ExpressRoute-krets och peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864374"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Arbetsflöden i ExpressRoute för kretsetablering och kretstillstånd
På den här sidan får du hjälp med konfigurationsarbetsflöden för tjänstetablering och routning på en hög nivå.

![kretsarbetsflöde](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Följande bild och motsvarande steg beskriver aktiviteterna för att etablera en ExpressRoute-krets från på slutning. 

1. Använd PowerShell för att konfigurera en ExpressRoute-krets. Följ instruktionerna i artikeln [Skapa ExpressRoute-kretsar](expressroute-howto-circuit-classic.md) för mer information.
2. Beställ anslutning från tjänsteleverantören. Den här processen varierar. Kontakta din anslutningsleverantör för mer information om hur du beställer anslutning.
3. Kontrollera att kretsen har etablerats genom att verifiera tillståndet för etablerar ExpressRoute-kretsen via PowerShell. 
4. Konfigurera routningsdomäner. Om anslutningsleverantören hanterar Layer 3-konfigurationen konfigurerar de routning för din krets. Om anslutningsleverantören endast erbjuder Layer 2-tjänster måste du konfigurera routning enligt riktlinjerna som beskrivs i [routningskraven](expressroute-routing.md) och [routningskonfigurationssidorna.](expressroute-howto-routing-classic.md)
   
   * Aktivera privat Azure-peering – Aktivera den här peeringen för att ansluta till virtuella datorer/molntjänster som distribueras i virtuella nätverk.

   * Aktivera Microsoft-peering – Aktivera detta för att komma åt Microsofts onlinetjänster, till exempel Office 365. Alla Azure PaaS-tjänster är tillgängliga via Microsoft-peering.
     
     > [!IMPORTANT]
     > Du måste se till att du använder en separat proxy / kant för att ansluta till Microsoft än den du använder för Internet. Om du använder samma kant för både ExpressRoute och Internet orsaka asymmetrisk routning och orsaka anslutningsbrott för nätverket.
     > 
     > 
     
     ![routningsarbetsflöden](./media/expressroute-workflows/routing-workflow.png)
5. Länka virtuella nätverk till ExpressRoute-kretsar - Du kan länka virtuella nätverk till din ExpressRoute-krets. Följ instruktionerna [för att länka virtuella nätverk](expressroute-howto-linkvnet-arm.md) till din krets. Dessa virtuella nätverk kan antingen vara i samma Azure-prenumeration som ExpressRoute-kretsen eller vara i en annan prenumeration.

## <a name="expressroute-circuit-provisioning-states"></a>Etablerande lägen för ExpressRoute-krets
Varje ExpressRoute-krets har två tillstånd:

* Etableringstillstånd för tjänsteleverantörer
* Status

Status representerar Microsofts etableringstillstånd. Den här egenskapen är aktiverad när du skapar en Expressroute-krets

Etableringstillståndet för anslutningsprovidern representerar tillståndet på anslutningsleverantörens sida. Det kan antingen vara *NotProvisioned,* *Etablering*eller *Etablering .* ExpressRoute-kretsen måste vara i ett etablerat tillstånd för att konfigurera peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Möjliga tillstånd av en ExpressRoute krets
I det här avsnittet visas möjliga tillstånd för en ExpressRoute-krets.

**Vid skapande tid**

ExpressRoute-kretsen rapporterar följande tillstånd när resurserna skapas.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**När anslutningsleverantören håller på att etablera kretsen**

ExpressRoute-kretsen rapporterar följande tillstånd medan anslutningsleverantören arbetar för att etablera kretsen.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**När anslutningsleverantören har slutfört etableringsprocessen**

ExpressRoute-kretsen rapporterar följande tillstånd när anslutningsleverantören har etablerat kretsen.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**När anslutningsleverantören avetableras avetableras**

Om ExpressRoute-kretsen behöver avetableras kommer kretsen att rapportera följande tillstånd när tjänsteleverantören har slutfört avetableringsprocessen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Du kan välja att återaktivera den om det behövs, eller köra PowerShell-cmdlets för att ta bort kretsen.  

> [!IMPORTANT]
> Det går inte att ta bort en krets när ServiceProviderProvisioningState etablerar eller etableras. Anslutningsleverantören måste avetablera kretsen innan den kan tas bort. Microsoft fortsätter att fakturera kretsen tills ExpressRoute-kretsresursen tas bort i Azure.
> 

## <a name="routing-session-configuration-state"></a>Konfigurationstillstånd för routningssession
BGP-etableringstillståndsrapporter om BGP-sessionen har aktiverats på Microsoft-kanten. Tillståndet måste vara aktiverat för att använda privat eller Microsoft-peering.

Det är viktigt att kontrollera BGP-sessionstillståndet, särskilt för Microsoft-peering. Förutom bgp-etableringstillståndet finns det ett annat tillstånd som kallas *annonserade offentliga prefixtillstånd*. Tillståndet annonserade offentliga prefix måste vara i *konfigurerat* tillstånd, både för att BGP-sessionen ska vara uppe och för att routningen ska fungera från på nytt. 

Om det annonserade offentliga prefixtillståndet är inställt på ett tillstånd som *krävs för validering* är BGP-sessionen inte aktiverad, eftersom de annonserade prefixen inte matchade AS-numret i något av routningsregistren. 

> [!IMPORTANT]
> Om tillståndet för annonserade offentliga prefix är i *manuellt valideringstillstånd* måste du öppna en supportbiljett med [Microsoft-support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och tillhandahålla bevis på att du äger de IP-adresser som annonseras tillsammans med det associerade autonoma systemnumret.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

