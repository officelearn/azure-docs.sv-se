---
title: Azure VMware Solution - DNS-vidarebefordran från privat moln till lokalt
description: Beskriver hur du aktiverar din CloudSimple Private Cloud DNS-server för att vidarebefordra sökning av lokala resurser
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961132"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Aktivera CloudSimple Private Cloud DNS-servrar för att vidarebefordra DNS-sökning av lokala resurser till dina DNS-servrar

Privata Cloud DNS-servrar kan vidarebefordra DNS-sökning för alla lokala resurser till dina DNS-servrar.  Om du aktiverar sökningen kan privata moln-vSphere-komponenter slå upp alla tjänster som körs i din lokala miljö och kommunicera med dem med fullständiga domännamn (FQDN).

## <a name="scenarios"></a>Scenarier 

Genom att vidarebefordra DNS-sökning för din lokala DNS-server kan du använda ditt privata moln för följande scenarier:

* Använd Private Cloud som en inställning för haveriberedskap för din lokala VMware-lösning
* Använda lokal Active Directory som identitetskälla för ditt privata moln vSphere
* Använd HCX för att migrera virtuella datorer från lokalt till privat moln

## <a name="before-you-begin"></a>Innan du börjar

En nätverksanslutning måste finnas från ditt privata molnnätverk till ditt lokala nätverk för att DNS-vidarebefordran ska fungera.  Du kan konfigurera nätverksanslutning med:

* [Ansluta från lokalt till CloudSimple med ExpressRoute](on-premises-connection.md)
* [Konfigurera en VPN-gateway för plats till plats](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

Brandväggsportar måste öppnas på den här anslutningen för att DNS-vidarebefordran ska fungera.  Portar som används är TCP-port 53 eller UDP-port 53.

> [!NOTE]
> Om du använder Site-to-Site VPN måste ditt lokala DNS-serverundernät läggas till som en del av lokala prefix.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Begär DNS-vidarekoppling från privat moln till lokalt

Om du vill aktivera DNS-vidarebefordran från Privat moln till lokalt skickar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)med följande information.

* Typ av problem: **Teknisk**
* Prenumeration: **Prenumeration där CloudSimple-tjänsten distribueras**
* Tjänst: **VMware-lösning från CloudSimple**
* Problemtyp: **Rådgivande eller Hur gör jag ...**
* Problemsubtyp: **Behöver du hjälp med NW**
* Ange domännamnet för din lokala domän i informationsfönstret.
* Ange en lista över dina lokala DNS-servrar som sökningen vidarebefordras från ditt privata moln i informationsfönstret.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om lokal brandväggskonfiguration](on-premises-firewall-configuration.md)
* [Lokal DNS-serverkonfiguration](on-premises-dns-setup.md)
