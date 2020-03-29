---
title: Vad är betrodda säkerhetspartner för Azure Firewall Manager (förhandsversion)
description: Lär dig mer om betrodda säkerhetspartner i Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436776"
---
# <a name="what-are-trusted-security-partners-preview"></a>Vad är betrodda säkerhetspartner (förhandsversion)?

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Med betrodda säkerhetspartner (förhandsversion)* i Azure Firewall Manager kan du använda dina välbekanta, bäst i raser, tredjepartssäkerhet som en tjänst (SECaaS) för att skydda Internetåtkomst för dina användare.

Med en snabb konfiguration kan du skydda ett nav med en säkerhetspartner som stöds och dirigera och filtrera Internet-trafik från virtuella nätverk (Virtuella nätverk) eller filialplatser inom en region. Detta görs med hjälp av automatisk rutthantering, utan att ställa in och hantera användardefinierade vägar (UDRs).

Du kan distribuera säkra hubbar som konfigurerats med den säkerhetspartner du väljer i flera Azure-regioner för att få anslutning och säkerhet för dina användare över hela världen i dessa regioner. Med möjligheten att använda säkerhetspartnerns erbjudande för Internet/SaaS-programtrafik och Azure-brandvägg för privat trafik i de skyddade naven kan du nu börja bygga din säkerhetsfördel på Azure som ligger nära dina globalt distribuerade användare och program.

För den här förhandsversionen är de säkerhetspartner som stöds **ZScaler** och **iboss**. Regioner som stöds är WestCentralUS, NorthCentralUS, WestUS, WestUS2 och EastUS.

![Betrodda säkerhetspartner](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Viktiga scenarier

Du kan använda säkerhetspartnerna för att filtrera Internet-trafik i följande scenarier:

- Virtuellt nätverk (VNet) till Internet

   Utnyttja avancerat användarmedvetna Internetskydd för dina molnarbetsbelastningar som körs på Azure.

- Förgrena sig till Internet

   Utnyttja din Azure-anslutning och globala distribution för att enkelt lägga till NSaaS-filtrering från tredje part för gren till Internet-scenarier. Du kan skapa ditt globala transitnätverk och säkerhetsfördelar med Azure Virtual WAN.

Följande scenarier stöds:
-   VNet till Internet via ett tredjepartspartnererbjudande.
-   Förgrena dig till Internet via ett tredjepartspartnererbjudande.
-   Förgrena dig till Internet via ett partnererbjudande från tredje part, resten av den privata trafiken (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) via Azure Firewall.

Följande scenario stöds inte:

- VNet till Internet via ett partnererbjudande kan inte kombineras med Azure-brandväggen för privat trafik. Se följande begränsningar.

## <a name="current-limitations"></a>Aktuella begränsningar

- För VNet till Internet kan du inte blanda lägga till Azure-brandväggen för privat trafik och ett partnererbjudande för Internet-trafik. Du kan antingen skicka Internet-trafik till Azure-brandväggen eller en tredjepartssäkerhetspartner som erbjuder i det skyddade virtuella navet, men inte båda. 
- Du kan distribuera högst en säkerhetspartner per virtuellt nav. Om du behöver ändra providern måste du ta bort den befintliga partnern och lägga till en ny.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Metodtips för filtrering av Internettrafik i säkra virtuella hubbar

Internettrafik omfattar vanligtvis webbtrafik. Men den innehåller också trafik som är avsedd för SaaS-program som Office 365 (O365) och Azure offentliga PaaS-tjänster som Azure Storage, Azure Sql och så vidare. Följande är rekommendationer om bästa praxis för hantering av trafik till dessa tjänster:

### <a name="handling-azure-paas-traffic"></a>Hantering av Azure PaaS-trafik
 
- Använd Azure-brandväggen för skydd om din trafik till största delen består av Azure PaaS och resursåtkomsten för dina program kan filtreras med IP-adresser, FQDN-taggar, tjänsttaggar eller FQDN-taggar.

- Använd en tredjepartspartnerlösning i dina hubbar om din trafik består av Åtkomst till SaaS-program, eller om du behöver användarmedveten filtrering (till exempel för dina VDI-arbetsbelastningar (Virtual Desktop Infrastructure) eller om du behöver avancerade internetfiltreringsfunktioner.

![Alla scenarier för Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Hantera Office 365-trafik (O365)

I globalt distribuerade filialplatsscenarier bör du omdirigera Office 365-trafik direkt till grenen innan du skickar den återstående Internet-trafiken till din Azure-säkrad hubb.

För Office 365 är nätverksfördröjning och prestanda avgörande för en lyckad användarupplevelse. För att uppnå dessa mål kring optimal prestanda och användarupplevelse måste kunderna implementera Direkt och lokal flykt för Office 365 innan de överväger att dirigera resten av Internet-trafiken via Azure.

[Office 365-principer](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) för nätverksanslutning kräver att viktiga Office 365-nätverksanslutningar dirigeras lokalt från användargrenen eller den mobila enheten och direkt via Internet till närmaste Microsoft-nätverkspunkt.

Dessutom är Office 365-anslutningar starkt krypterade för sekretess och använder effektiva, proprietära protokoll av prestandaskäl. Detta gör det opraktiskt och effektfullt att utsätta dessa anslutningar för traditionella säkerhetslösningar på nätverksnivå. Av dessa skäl rekommenderar vi starkt att kunder skickar Office 365-trafik direkt från filialer innan de skickar resten av trafiken via Azure. Microsoft samarbetar med flera SD-WAN-lösningsleverantörer, som integrerar med Azure och Office 365 och gör det enkelt för kunder att aktivera Direkt och lokal Internet-breakout för Office 365. Mer information finns i [Hur ställer jag in mina O365-principer via Virtuellt WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Nästa steg

[Distribuera ett betrott säkerhetserbjudande i ett skyddat nav med hjälp av Azure Firewall Manager](deploy-trusted-security-partner.md).
