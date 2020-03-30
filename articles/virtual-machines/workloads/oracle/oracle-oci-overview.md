---
title: Integrera Microsoft Azure med Oracles molninfrastruktur | Microsoft-dokument
description: Lär dig mer om lösningar som integrerar Oracle-appar som körs på Microsoft Azure med databaser i Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: e1249913300be532cc6514f1478bbc6f4183c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300561"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle-programlösningar som integrerar Microsoft Azure och Oracle Cloud Infrastructure (förhandsversion)

Microsoft och Oracle samarbetar för att ge låg latens, hög genomströmning cross-cloud anslutning, så att du kan dra nytta av det bästa av båda molnen. 

Med den här anslutningsövergripande molnanslutningen kan du partitionera ett program på flera nivåer för att köra din databasnivå på Oracle Cloud Infrastructure (OCI) och programmet och andra nivåer på Microsoft Azure. Upplevelsen liknar att köra hela lösningsstacken i ett enda moln. 

> [!IMPORTANT]
> Den här cross-cloud-funktionen är för närvarande i förhandsversion och [begränsningar gäller](#region-availability). För att upprätta anslutning med låg latens mellan Azure och OCI måste din Azure-prenumeration först aktiveras för den här funktionen. Du måste registrera dig i förhandsgranskningen genom att fylla i det här korta [undersökningsformuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats. Du kan inte använda funktionen förrän du får en bekräftelse via e-post. Du kan också kontakta din Microsoft-representant för att aktiveras för den här förhandsversionen. Åtkomst till förhandsversionen är beroende av tillgänglighet och begränsas av Microsoft efter eget gottfinnande. Slutförandet av undersökningen garanterar inte tillgång. Den här förhandsversionen tillhandahålls utan ett servicenivåavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i de [kompletterande användarvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för Microsoft Azure Previews. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

Om du är intresserad av att distribuera Oracle-lösningar helt på Azure-infrastruktur läser du [Oracles VM-avbildningar och deras distribution på Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Scenarioöversikt

Anslutning mellan moln ger dig en lösning för att köra Oracles branschledande program och dina egna anpassade program på virtuella Azure-datorer samtidigt som du kan dra nytta av fördelarna med värdbaserade databastjänster i OCI. 

Program som du kan köra i en konfiguration över flera moln är:

* Svit E-Business
* JD Edwards EnterpriseOne
* Peoplesoft
* Oracle Retail-program
* Oracle Hyperion ekonomisk förvaltning

Följande diagram är en översikt på hög nivå av den anslutna lösningen. För enkelhetens skull visar diagrammet bara en programnivå och en datanivå. Beroende på programarkitekturen kan din lösning innehålla ytterligare nivåer, till exempel en webbnivå i Azure. Mer information finns i följande avsnitt.

![Översikt över Azure OCI-lösning](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Regionens tillgänglighet 

Anslutning mellan moln är begränsad till följande regioner:
* Azure East US (eastus) & OCI Ashburn (östra USA)
* Azure Uk South (uksouth) & OCI London (Södra Storbritannien)
* Azure Canada Central (canadacentral) & OCI Toronto (Sydöstra Kanada)
* Azure West Europe (westeurope) & OCI Amsterdam (Nederländerna Nordväst)

## <a name="networking"></a>Nätverk

Företagskunder väljer ofta att diversifiera och distribuera arbetsbelastningar över flera moln av olika affärs- och driftsskäl. För att diversifiera kopplar kunder samman molnnätverk med internet, IPSec VPN eller använder molnleverantörens direktanslutningslösning via ditt lokala nätverk. Sammanlänkande molnnätverk kan kräva betydande investeringar i tid, pengar, design, upphandling, installation, testning och drift. 

Oracle och Microsoft har aktiverat en integrerad upplevelse med flera moln för att möta dessa kundutmaningar. Nätverk över moln upprättas genom att ansluta en [ExpressRoute-krets](../../../expressroute/expressroute-introduction.md) i Microsoft Azure med en [FastConnect-krets](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) i OCI. Den här anslutningen är möjlig om en Azure ExpressRoute-peering-plats är i närheten av eller på samma peering-plats som OCI FastConnect. Den här inställningen möjliggör säker och snabb anslutning mellan de två molnen utan behov av en mellanliggande tjänsteleverantör.

Med ExpressRoute och FastConnect kan kunder peer ett virtuellt nätverk i Azure med ett virtuellt molnnätverk i OCI, förutsatt att det privata IP-adressutrymmet inte överlappar varandra. Peering de två nätverken tillåter en resurs i det virtuella nätverket att kommunicera med en resurs i OCI virtuella molnnätverket som om de båda är i samma nätverk.

## <a name="network-security"></a>Nätverkssäkerhet

Nätverkssäkerhet är en viktig komponent i alla företagsprogram och är central för den här lösningen med flera moln. All trafik som går över ExpressRoute och FastConnect passerar över ett privat nätverk. Den här konfigurationen möjliggör säker kommunikation mellan ett virtuellt Azure-nätverk och ett virtuellt Oracle-molnnätverk. Du behöver inte ange en offentlig IP-adress till några virtuella datorer i Azure. På samma sätt behöver du inte en internet gateway i OCI. All kommunikation sker via maskinernas privata IP-adress.

Dessutom kan du ställa in [säkerhetslistor](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) i OCI:s virtuella molnnätverk och säkerhetsregler (kopplade till [Azure-nätverkssäkerhetsgrupper](../../../virtual-network/security-overview.md)). Använd dessa regler för att styra trafiken som flödar mellan datorer i de virtuella nätverken. Nätverkssäkerhetsregler kan läggas till på datornivå, på undernätsnivå och på den virtuella nätverksnivån.
 
## <a name="identity"></a>Identitet

Identitet är en av grundpelarna i partnerskapet mellan Microsoft och Oracle. Betydande arbete har gjorts för att integrera [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) med Azure Active [Directory](../../../active-directory/index.yml) (Azure AD). Azure AD är Microsofts molnbaserade identitets- och åtkomsthanteringstjänst. Det hjälper användarna att logga in och komma åt olika resurser. Med Azure AD kan du också hantera dina användare och deras behörigheter.

För närvarande kan den här integreringen hantera på en central plats, vilket är Azure Active Directory. Azure AD synkroniserar alla ändringar i katalogen med motsvarande Oracle-katalog och används för enkel inloggning till Oracle-lösningar mellan moln.

## <a name="next-steps"></a>Nästa steg

Kom igång med ett [nätverk mellan moln](configure-azure-oci-networking.md) mellan Azure och OCI. 

Mer information och faktablad om OCI finns i [Oracle Cloud-dokumentationen.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
