---
title: Integrera Microsoft Azure med Oracle-Molninfrastruktur | Microsoft Docs
description: Läs mer om lösningar som integrerar Oracle-program som körs på Microsoft Azure med databaser i molnet infrastruktur OCI (Oracle).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 5a60e41d3195c0f7d88fd3ba14336d693d2f528e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446688"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle-programlösningar integrera Microsoft Azure och Oracle Cloud Infrastructure (förhandsversion)

Microsoft och Oracle samarbetar för att ge låg latens, högt dataflöde molnöverskridande anslutningen, så att du kan dra nytta av bäst av båda molnen. 

Med den här anslutningen mellan moln kan partitionera du ett flerskiktat program att köra dina databasnivån på molnet infrastruktur OCI (Oracle), och programmet och andra nivåer på Microsoft Azure. Upplevelsen är liknande körs hela lösningen i ett enda moln. 

> [!IMPORTANT]
> Den här molnöverskridande-funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

Om du är intresserad av att distribuera Oracle-lösningar som helt och hållet i Azure-infrastrukturen kan se [Oracle VM-avbildningar och deras distribution på Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Scenarioöversikt

Anslutningen mellan moln är en lösning som du kan köra Oracles branschledande program och dina egna anpassade program på Azure virtuella datorer samtidigt som drar nytta av fördelarna av värdbaserade databastjänster i OCI. 

Du kan köra i en konfiguration med molnöverskridande-programmen innehåller:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail-program
* Oracle Hyperion finansiell hantering

Följande diagram är en översikt över ansluten lösningen. För enkelhetens skull visar diagrammet endast en programnivå och en datanivå. Beroende på programarkitektur, kan din lösning omfatta fler nivåer, till exempel en webbnivå i Azure. Mer information finns i följande avsnitt.

![Översikt över lösning för Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Molnöverskridande anslutningen i en förhandsversion är begränsad till regionen Azure östra USA (eastus) och regionen OCI Ashburn (USA-ashburn-1).

## <a name="networking"></a>Nätverk

Enterprise-kunder väljer ofta att distribuera och distribuera arbetsbelastningar över flera moln för olika företag och operativa orsaker. Om du vill distribuera, sammankoppling kunder molnnätverk via internet, IPSec VPN eller med hjälp av molnleverantörens direktanslutning lösning via ditt lokala nätverk. Interconnecting molnnätverk kan kräva betydande investeringar i tid, pengar, design, inköp, installation, testning och drift. 

Om du vill hantera dessa kundutmaningar, har Oracle och Microsoft aktiverat en integrerad upplevelse i flera moln. Nätverk mellan moln upprättas genom att ansluta en [ExpressRoute](../../../expressroute/expressroute-introduction.md) -kretsen i Microsoft Azure med en [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) -kretsen i OCI. Den här anslutningen är möjligt där en peering Azure ExpressRoute-plats är i närhet eller i samma peering plats som OCI FastConnect. Den här konfigurationen möjliggör säker och snabb anslutning mellan de två moln utan att behöva en mellanliggande-leverantör.

Med ExpressRoute och FastConnect kan kunder kan peer-koppla ett virtuellt nätverk i Azure med ett virtuellt moln-nätverk i OCI, förutsatt att det privata IP-adressutrymmet inte överlappar. Peering i två nätverk kan en resurs i det virtuella nätverket kommunicera till en resurs i nätverket OCI virtuellt moln som om de är båda i samma nätverk.

## <a name="network-security"></a>Nätverkssäkerhet

Nätverkssäkerhet är en fundamental komponent i alla företagsprogram och är centrala för den här lösningen med flera moln. All trafik som skickas via ExpressRoute och FastConnect skickar via ett privat nätverk. Den här konfigurationen möjliggör säker kommunikation mellan ett Azure-nätverk och ett nätverk för Oracle-virtuellt moln. Du behöver inte ange en offentlig IP-adress till alla virtuella datorer i Azure. Du behöver inte på samma sätt kan en Internetgateway i OCI. All kommunikation sker via privata IP-adress för datorer.

Dessutom kan du ställa in [säkerhetslistor](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) på dina OCI virtuellt moln nätverks- och säkerhetsfunktioner regler (ansluten till Azure [nätverkssäkerhetsgrupper](../../../virtual-network/security-overview.md)). Använd de här reglerna för att styra den trafik som passerar mellan datorer i virtuella nätverk. Nätverkssäkerhetsregler kan läggas till på en datornivå, på en undernätverksnivå, samt på nivån för virtuella nätverket.
 
## <a name="identity"></a>Identitet

Identiteten är en av core grundpelare för partnerskap mellan Microsoft och Oracle. Betydande arbete har gjorts för att integrera [Oracle Cloud Identitetstjänst](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) med [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD är Microsofts molnbaserade identitets- och management-tjänsten. Det hjälper dina användare logga in och komma åt olika resurser. Azure AD kan du hantera användare och deras behörigheter.

För närvarande kan den här integrationen du hantera en central plats, vilket är Azure Active Directory. Azure AD synkroniseras alla ändringar i katalogen med motsvarande Oracle-katalog och används för enkel inloggning till molnöverskridande Oracle-lösningar.

## <a name="next-steps"></a>Nästa steg

Kom igång med en [molnöverskridande nätverk](configure-azure-oci-networking.md) mellan Azure och OCI. 

Mer information och White Paper-faktablad om OCI finns i den [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentation.
