---
title: 'Azure-ExpressRoute: arbets flöden för krets konfiguration'
description: På den här sidan visas arbets flöden för konfiguration av ExpressRoute-krets och peering
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864374"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Arbetsflöden i ExpressRoute för kretsetablering och kretstillstånd
Den här sidan vägleder dig genom tjänst etableringen och konfigurations arbets flöden för routning på en hög nivå.

![krets arbets flöde](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Följande bild och motsvarande steg beskriver aktiviteterna för att etablera en ExpressRoute-krets från slut punkt till slut punkt. 

1. Använd PowerShell för att konfigurera en ExpressRoute-krets. Följ anvisningarna i artikeln [skapa ExpressRoute-kretsar](expressroute-howto-circuit-classic.md) för mer information.
2. Beställ anslutningar från tjänst leverantören. Den här processen varierar. Kontakta din anslutnings leverantör om du vill ha mer information om hur du beställer anslutningar.
3. Kontrol lera att kretsen har etablerats genom att verifiera ExpressRoute-kretsens etablerings status via PowerShell. 
4. Konfigurera routningsdomäner. Om din anslutnings leverantör hanterar Layer 3-konfiguration, konfigureras routning för din krets. Om din anslutnings leverantör bara erbjuder Layer 2-tjänster, måste du konfigurera routning enligt rikt linjerna som beskrivs i sidorna för [routnings krav](expressroute-routing.md) och [konfiguration av routning](expressroute-howto-routing-classic.md) .
   
   * Aktivera Azures privata peering – aktivera peer koppling för att ansluta till virtuella datorer/moln tjänster som distribueras i virtuella nätverk.

   * Aktivera Microsoft-peering – aktivera detta för att få åtkomst till Microsoft onlinetjänster, till exempel Office 365. Alla Azure PaaS-tjänster är tillgängliga via Microsoft-peering.
     
     > [!IMPORTANT]
     > Du måste se till att du använder en separat proxy/Edge för att ansluta till Microsoft än den som du använder för Internet. Att använda samma gräns för både ExpressRoute och Internet kommer att orsaka asymmetrisk Routning och orsaka anslutnings avbrott för nätverket.
     > 
     > 
     
     ![routa arbets flöden](./media/expressroute-workflows/routing-workflow.png)
5. Länka virtuella nätverk till ExpressRoute-kretsar – du kan länka virtuella nätverk till din ExpressRoute-krets. Följ anvisningarna [för att länka virtuella nätverk](expressroute-howto-linkvnet-arm.md) till din krets. Dessa virtuella nätverk kan antingen finnas i samma Azure-prenumeration som ExpressRoute-kretsen eller vara i en annan prenumeration.

## <a name="expressroute-circuit-provisioning-states"></a>Etablerings tillstånd för ExpressRoute-krets
Varje ExpressRoute-krets har två tillstånd:

* Etablerings status för tjänste leverantör
* Status

Status representerar Microsofts etablerings status. Den här egenskapen anges till aktive rad när du skapar en ExpressRoute-krets

Etablerings statusen för anslutnings leverantören representerar statusen på anslutnings leverantörens sida. Det kan antingen vara *NotProvisioned*, *etablering*eller *etablerad*. ExpressRoute-kretsen måste vara i ett tillhandahållet tillstånd för att konfigurera peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Möjliga tillstånd för en ExpressRoute-krets
I det här avsnittet visas en lista över möjliga tillstånd för en ExpressRoute-krets.

**Vid skapande tillfället**

ExpressRoute-kretsen rapporterar följande tillstånd när en resurs skapas.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**När anslutnings leverantören håller på att allokera kretsen**

ExpressRoute-kretsen kommer att rapportera följande tillstånd när anslutnings leverantören arbetar för att etablera kretsen.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**När anslutnings leverantören har slutfört etablerings processen**

ExpressRoute-kretsen rapporterar följande tillstånd när anslutnings leverantören har kunnat allokera kretsen.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**När anslutnings leverantören avetablerar kretsen**

Om ExpressRoute-kretsen behöver avetableras, kommer kretsen att rapportera följande tillstånd när tjänste leverantören har slutfört avetablerings processen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Du kan välja att återaktivera den om det behövs eller köra PowerShell-cmdletar för att ta bort kretsen.  

> [!IMPORTANT]
> Det går inte att ta bort en krets när ServiceProviderProvisioningState är etablerad eller etablerad. Anslutnings leverantören måste avetablera kretsen innan den kan tas bort. Microsoft fortsätter att debitera kretsen tills ExpressRoute-krets-resursen tas bort i Azure.
> 

## <a name="routing-session-configuration-state"></a>Konfigurations tillstånd för routningsdomän
Tillstånds rapporter för BGP-etablering om BGP-sessionen har Aktiver ATS på Microsoft Edge. Status måste vara aktive rad för att använda privat eller Microsoft-peering.

Det är viktigt att kontrol lera BGP-sessionstillstånd särskilt för Microsoft-peering. Förutom BGP-etablerings status är det ett annat tillstånd som kallas *annonserat offentlig prefix*. Det annonserade offentliga prefixet måste vara i det *konfigurerade* läget, båda för att BGP-sessionen ska vara igång och för att routningen ska fungera från slut punkt till slut punkt. 

Om det annonserade offentliga prefixet är inställt på ett tillstånd som *krävs för validering* , aktive ras inte BGP-sessionen eftersom de annonserade prefixen inte MATCHAde som-antalet i någon av vägvals registren. 

> [!IMPORTANT]
> Om det annonserade offentliga prefixet är i *manuellt validerings* tillstånd måste du öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och tillhandahålla bevis på att du äger IP-adresserna som annonseras tillsammans med det tillhör ande autonoma system numret.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

