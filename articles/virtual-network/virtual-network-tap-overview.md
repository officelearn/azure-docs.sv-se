---
title: Översikt över Azure-virtuellt nätverk TAP | Microsoft-dokument
description: Läs mer om det virtuella nätverket TAP. Med virtuellt nätverk TAP får du en djup kopia av nätverkstrafik för virtuella datorer som kan strömmas till en paketinsamlare.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279616"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP

Med Azure-virtuellt nätverk TAP (Terminal Access Point) kan du kontinuerligt strömma din virtuella datornätverkstrafik till ett nätverkspaketinsamlare eller analysverktyg. Insamlaren eller analysverktyget tillhandahålls av en [virtuell nätverksinstallationspartner.](https://azure.microsoft.com/solutions/network-appliances/) En lista över partnerlösningar som har validerats för att fungera med det virtuella nätverket TAP finns i [partnerlösningar](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Virtuellt nätverk TAP är för närvarande i förhandsversion i alla Azure-regioner. Om du vill använda virtuellt nätverk TAP måste du <azurevnettap@microsoft.com> registrera dig i förhandsversionen genom att skicka ett e-postmeddelande till med ditt prenumerations-ID. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats. Du kan inte använda funktionen förrän du får en bekräftelse via e-post. Den här förhandsversionen tillhandahålls utan ett servicenivåavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i de [kompletterande användarvillkoren för Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="virtual-network-tap-partner-solutions"></a>TAP-partnerlösningar för virtuellt nätverk

### <a name="network-packet-brokers"></a>Nätverkspaketmäklare

- [Big Switch Stor Övervakning Tyg](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prismor](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Säkerhetsanalys, prestandahantering för nätverk/program

- [Vaken säkerhet](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Moln](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace (mörkare)](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersäkerhet](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flödesmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Flodbädd SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® plattform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Följande bild visar hur virtuellt nätverk TAP fungerar. Du kan lägga till en TAP-konfiguration i ett [nätverksgränssnitt](virtual-network-network-interface.md) som är kopplat till en virtuell dator som distribueras i det virtuella nätverket. Målet är en virtuell nätverks-IP-adress i samma virtuella nätverk som det övervakade nätverksgränssnittet eller ett [peer-virtuellt](virtual-network-peering-overview.md) nätverk. Collector-lösningen för virtuellt nätverk TAP kan distribueras bakom en Azure Internal Load Balancer för hög tillgänglighet. Information om hur du utvärderar distributionsalternativ för enskilda lösningar finns i [partnerlösningar](#virtual-network-tap-partner-solutions).

![Så här fungerar virtuellt nätverk TAP](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Krav

Innan du skapar ett virtuellt nätverk TAP måste du ha fått ett bekräftelsemeddelande om att du är registrerad i förhandsversionen och ha en eller flera virtuella datorer skapade med hjälp av Azure Resource Manager-distributionsmodellen och en partnerlösning för att samla TAP-trafiken i samma azure-region. [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Om du inte har någon partnerlösning i det virtuella nätverket kan du se [partnerlösningar](#virtual-network-tap-partner-solutions) för att distribuera en. Du kan använda samma TAP-resurs för virtuellt nätverk för att aggregera trafik från flera nätverksgränssnitt i samma eller olika prenumerationer. Om de övervakade nätverksgränssnitten finns i olika prenumerationer måste prenumerationerna associeras till samma Azure Active Directory-klientorganisation. Dessutom kan de övervakade nätverksgränssnitten och målslutpunkten för att samla TAP-trafiken vara i peered virtuella nätverk i samma region. Om du använder den här distributionsmodellen ska du se till att den [virtuella nätverks peer-datorn](virtual-network-peering-overview.md) är aktiverad innan du konfigurerar virtuellt nätverk TAP.

## <a name="permissions"></a>Behörigheter

De konton som du använder för att tillämpa TAP-konfigurationen i nätverksgränssnitt måste tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas nödvändiga åtgärder från följande tabell:

| Åtgärd | Namn |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Krävs för att skapa, uppdatera, läsa och ta bort en virtuell RESURS för TAP-nätverk |
| Microsoft.Network/networkInterfaces/read | Krävs för att läsa nätverksgränssnittsresursen som TAP ska konfigureras på |
| Microsoft.Network/tapConfigurations/* | Krävs för att skapa, uppdatera, läsa och ta bort TAP-konfigurationen i ett nätverksgränssnitt |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar ett virtuellt nätverk TAP](tutorial-tap-virtual-network-cli.md).
