---
title: Översikt över Azure virtual network-TRYCK | Microsoft Docs
description: Läs mer om virtuella nätverks-TAP. Virtual network ger trycker du på en djup kopia av VM-nätverkstrafik som kan strömmas för insamling och paket.
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
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571103"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP

Azure-nätverk TRYCK (Terminal åtkomstpunkt) kan du kontinuerligt stream dina VM-nätverkstrafik till en insamlare eller analytics verktyg för nätverkspaket. Verktyget insamlare eller analytics tillhandahålls av en [virtuell nätverksinstallation](https://azure.microsoft.com/solutions/network-appliances/) partner. En lista över partnerlösningar som godkänts för att fungera med virtuella nätverks-TAP finns i [partnerlösningar](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Virtuellt nätverk TRYCK förhandsvisas just nu i alla Azure-regioner. Om du vill använda virtuella nätverks-TAP som du måste registrera i förhandsgranskningen genom att skicka ett e-postmeddelande till <azurevnettap@microsoft.com> med ditt prenumerations-ID. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats. Du inte kan använda funktionen tills du får ett e-postmeddelande med bekräftelse. Den här förhandsversionen tillhandahålls utan serviceavtal och ska inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Se den [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) information.

## <a name="virtual-network-tap-partner-solutions"></a>Virtuellt nätverk TRYCK partnerlösningar

### <a name="network-packet-brokers"></a>Paket asynkrona meddelandeköer för nätverk

- [Big växel Big övervakning Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Säkerhetsanalyser, nätverk/hantering av programprestanda

- [Aktiva säkerhet](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersäkerhet](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [RSA NetWitness® plattform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Följande bild visar hur virtuella nätverks-TRYCK fungerar. Du kan lägga till en konfiguration för TRYCK på en [nätverksgränssnittet](virtual-network-network-interface.md) som är kopplad till en virtuell dator distribueras i det virtuella nätverket. Målet är en IP-adress för virtuellt nätverk i samma virtuella nätverk som det övervakade nätverksgränssnittet eller en [peer-kopplat virtuellt](virtual-network-peering-overview.md) nätverk. Insamlaren lösningen för virtuella nätverks-TAP kan distribueras bakom en [Azure intern belastningsutjämnare](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) för hög tillgänglighet. Om du vill utvärdera distributionsalternativ för enskilda lösningen, se [partnerlösningar](#virtual-network-tap-partner-solutions).

![Hur virtuella nätverk TRYCK fungerar](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du skapar en virtuell nätverks-TAP måste du ha fått en bekräftelse e-post som du har registrerat i förhandsversionen och har en eller flera virtuella datorer som skapats med [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) distributionsmodellen och en partner lösning för att sammanställa och tryck sedan på trafik i samma azure-region. Om du inte har en partnerlösning i ditt virtuella nätverk kan du läsa [partnerlösningar](#virtual-network-tap-partner-solutions) att distribuera en. Du kan använda samma virtuella nätverk trycker du på resursen att samla trafik från flera nätverksgränssnitt i samma eller olika prenumerationer. Om de övervakade nätverksgränssnitt finns i olika prenumerationer, måste prenumerationerna associeras till samma Azure Active Directory-klient. Övervakade nätverksgränssnitt och målslutpunkten för sammanställning av TRYCK trafiken kan dessutom finnas i peer-kopplade virtuella nätverk i samma region. Om du använder denna distributionsmodell kontrollerar du att den [virtuell nätverkspeering](virtual-network-peering-overview.md) är aktiverad innan du konfigurerar virtuella nätverks-TAP.

## <a name="permissions"></a>Behörigheter

De konton som du använder för att tillämpa konfigurationen för TRYCK på nätverksgränssnitt måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas åtgärderna som krävs i följande tabell:

| Åtgärd | Namn |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Krävs för att skapa, uppdatera, läsa och ta bort ett virtuellt nätverk trycker du på resursen |
| Microsoft.Network/networkInterfaces/read | Krävs för att läsa den nätverksresurs gränssnitt som konfigureras och tryck sedan på |
| Microsoft.Network/tapConfigurations/* | Krävs för att skapa, uppdatera, läsa och ta bort konfigurationen av TRYCK på ett nätverksgränssnitt |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en virtuell nätverks-TAP](tutorial-tap-virtual-network-cli.md).
