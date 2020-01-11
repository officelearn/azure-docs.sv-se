---
title: 'Azure-ExpressRoute: arbets flöden för krets konfiguration'
description: Den här sidan visas arbetsflödena för att konfigurera ExpressRoute-kretsen och peer-kopplingar
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864374"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-arbetsflöden för kretsetablering och kretstillstånd
Den här sidan vägleder dig genom tjänsten etablering och routning configuration arbetsflöden på hög nivå.

![kretsen arbetsflöde](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Följande bild och motsvarande steg beskriver aktiviteterna för att etablera en ExpressRoute-krets från slut punkt till slut punkt. 

1. Konfigurera en ExpressRoute-krets med hjälp av PowerShell. Följ instruktionerna i den [skapa ExpressRoute-kretsar](expressroute-howto-circuit-classic.md) nedan för mer information.
2. Ordning anslutningen från tjänstleverantören. Den här processen varierar. Kontakta din anslutningsleverantör för mer information om hur du beställer anslutning.
3. Se till att kretsen har etablerats har genom att verifiera ExpressRoute-kretsen Etableringsstatus via PowerShell. 
4. Konfigurera routningsdomäner. Om din anslutnings leverantör hanterar Layer 3-konfiguration, konfigureras routning för din krets. Om din anslutnings leverantör bara erbjuder Layer 2-tjänster, måste du konfigurera routning enligt rikt linjerna som beskrivs i sidorna för [routnings krav](expressroute-routing.md) och [konfiguration av routning](expressroute-howto-routing-classic.md) .
   
   * Aktiverar Azures privata peering – aktivera den här peering för att ansluta till virtuella datorer / molntjänster som distribuerats i virtuella nätverk.

   * Aktivera Microsoft-peering – aktivera detta för att få åtkomst till Microsoft onlinetjänster, till exempel Office 365. Alla Azure PaaS-tjänster är tillgängliga via Microsoft-peering.
     
     > [!IMPORTANT]
     > Du måste se till att du använder en separat proxyserver / edge för att ansluta till Microsoft än som du använder för Internet. Använder samma kant för både ExpressRoute och Internet orsaka asymmetrisk Routning och orsaka avbrott i anslutningen för ditt nätverk.
     > 
     > 
     
     ![Routning arbetsflöden](./media/expressroute-workflows/routing-workflow.png)
5. Länka virtuella nätverk till ExpressRoute-kretsar – du kan länka virtuella nätverk till ExpressRoute-kretsen. Följ instruktionerna [att länka virtuella nätverk](expressroute-howto-linkvnet-arm.md) för din krets. Dessa virtuella nätverk kan antingen vara i samma Azure-prenumeration som ExpressRoute-krets eller kan vara i en annan prenumeration.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-krets Etableringsstatus
Varje ExpressRoute-krets har två lägen:

* Etableringsstatus för service provider
* Status

Status representerar Microsofts Etableringsstatus. Den här egenskapen är inställd på aktiverad när du skapar en Expressroute-krets

Etableringsstatus för anslutning providern representerar tillstånd i anslutningsleverantörens sida. Det kan antingen vara *NotProvisioned*, *etablering*, eller *etablerad*. ExpressRoute-kretsen måste vara i ett tillhandahållet tillstånd för att konfigurera peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Möjliga tillstånd för en ExpressRoute-krets
I det här avsnittet visas en lista över möjliga tillstånd för en ExpressRoute-krets.

**Vid skapandet**

ExpressRoute-kretsen rapporterar följande tillstånd när en resurs skapas.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**När anslutningsleverantör är processen för att etablera kretsen**

ExpressRoute-kretsen kommer att rapportera följande tillstånd när anslutnings leverantören arbetar för att etablera kretsen.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**När connectivity-leverantör har slutfört etableringsprocessen**

ExpressRoute-kretsen rapporterar följande tillstånd när anslutnings leverantören har kunnat allokera kretsen.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**När anslutningsleverantör håller på att avetablera kretsen**

Om ExpressRoute-kretsen behöver avetableras, kommer kretsen att rapportera följande tillstånd när tjänste leverantören har slutfört avetablerings processen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Du kan välja att aktivera den igen om det behövs, eller kör PowerShell-cmdletar för att ta bort kretsen.  

> [!IMPORTANT]
> Det går inte att ta bort en krets när ServiceProviderProvisioningState är etablerad eller etablerad. Anslutnings leverantören måste avetablera kretsen innan den kan tas bort. Microsoft fortsätter att debitera kretsen tills ExpressRoute-krets-resursen tas bort i Azure.
> 

## <a name="routing-session-configuration-state"></a>Routning sessionstillstånd för konfiguration
Tillstånds rapporter för BGP-etablering om BGP-sessionen har Aktiver ATS på Microsoft Edge. Status måste vara aktive rad för att använda privat eller Microsoft-peering.

Det är viktigt att kontrollera tillståndet för BGP-sessionen särskilt för Microsoft-peering. Förutom BGP Etableringsstatus, det finns ett annat tillstånd som kallas *annonserade offentliga prefix tillstånd*. Det annonserade offentliga prefixet måste vara i det *konfigurerade* läget, båda för att BGP-sessionen ska vara igång och för att routningen ska fungera från slut punkt till slut punkt. 

Om tillståndet annonserade offentliga prefix har angetts till en *verifiering krävs* tillstånd, BGP-sessionen inte är aktiverad, eftersom de annonserade prefix inte matchade det AS-nummer i någon av routningsregister. 

> [!IMPORTANT]
> Om det annonserade offentliga prefixet är i *manuellt validerings* tillstånd måste du öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och tillhandahålla bevis på att du äger IP-adresserna som annonseras tillsammans med det tillhör ande autonoma system numret.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

