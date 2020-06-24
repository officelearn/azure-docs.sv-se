---
title: Vad är Azure Firewall Manager Security partner providers (för hands version)
description: Lär dig mer om Azure Firewall Managers leverantörer av säkerhets partner
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: victorh
ms.openlocfilehash: 3d430deae191fbc9f9ab5bbbc2b83ee4640dd831
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791541"
---
# <a name="what-are-security-partner-providers-preview"></a>Vad är leverantörer av säkerhets partner (för hands version)?

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Leverantörer av säkerhets partner (för hands version)* i Azure Firewall Manager gör att du kan använda dina välkända, bästa SECaaS-och-erbjudanden (service Security as a Service) för att skydda Internet åtkomsten för dina användare.

Med en snabb konfiguration kan du skydda en hubb med en säkerhets partner som stöds och dirigera och filtrera Internet trafik från dina virtuella nätverk (virtuella nätverk) eller filial platser inom en region. Detta görs med hjälp av automatisk väg hantering utan att konfigurera och hantera användardefinierade vägar (UDR).

Du kan distribuera skyddade hubbar som kon figurer ATS med valfri säkerhets partner i flera Azure-regioner för att få anslutning och säkerhet för dina användare överallt i världen. Med möjligheten att använda säkerhets partner erbjudandet för Internet-SaaS program trafik och Azure-brandväggen för privat trafik i de skyddade hubbarna kan du nu börja bygga din säkerhets gräns på Azure som är nära dina globalt distribuerade användare och program.

I den här för hands versionen är de säkerhets partner som stöds **ZScaler**, **Check Point**och **iboss**. Regioner som stöds är WestCentralUS, Usanorracentrala, väst, WestUS2 och öster.

![Säkerhetspartnerprovidrar](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Viktiga scenarier

Du kan använda säkerhets partner för att filtrera Internet trafik i följande scenarier:

- Virtual Network (VNet) till Internet

   Använd avancerad användar medveten Internet skydd för dina moln arbets belastningar som körs på Azure.

- Förgrena till Internet

   Dra nytta av din Azure-anslutning och global distribution för att enkelt lägga till NSaaS-filtrering från tredje part för filial till Internet scenarier. Du kan bygga ditt globala överförings nätverk och säkerhets Edge med Azure Virtual WAN.

Följande scenarier stöds:
-   VNet till Internet via ett partner erbjudande från tredje part.
-   Förgrena till Internet via ett partner erbjudande från tredje part.
-   Gren till Internet via ett partner erbjudande från tredje part, resten av den privata trafiken (eker-till-ekrar, ekrar-till-förgreningar, gren-till-ekrar) via Azure Firewall.

Följande scenario stöds inte:

- VNet till Internet via ett partner erbjudande kan inte kombineras med Azure-brandväggen för privat trafik. Se följande begränsningar.

## <a name="current-limitations"></a>Aktuella begränsningar

- För VNet till Internet kan du inte blanda lägga till Azure-brandvägg för privat trafik och ett partner erbjudande för Internet trafik. Du kan antingen skicka Internet trafik till Azure-brandväggen eller en säkerhets partner från tredje part i den säkra virtuella hubben, men inte båda. 
- Du kan distribuera högst en säkerhets partner per virtuell hubb. Om du behöver ändra providern måste du ta bort den befintliga partnern och lägga till en ny.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Metod tips för Internet trafik filtrering i skyddade virtuella hubbar

Internet trafiken omfattar vanligt vis webb trafik. Men det inkluderar även trafik som är avsedd för SaaS-program som Office 365 (O365) och Azures offentliga PaaS-tjänster som Azure Storage, Azure SQL och så vidare. Följande är rekommendationer för att hantera trafik till dessa tjänster:

### <a name="handling-azure-paas-traffic"></a>Hantera Azure PaaS-trafik
 
- Använd Azure-brandväggen för skydd om din trafik främst består av Azure-PaaS och resurs åtkomsten för dina program kan filtreras med hjälp av IP-adresser, FQDN: er, service märken eller FQDN-taggar.

- Använd en partner lösning från tredje part i dina hubbar om din trafik består av SaaS-programåtkomst, eller om du behöver användar medveten filtrering (till exempel för dina VDI-arbetsbelastningar för Virtual Desktop Infrastructure) eller om du behöver avancerade funktioner för Internet filtrering.

![Alla scenarier för Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Hantera Office 365-trafik (O365)

I scenarier för globalt distribuerade gren platser bör du omdirigera Office 365-trafik direkt till grenen innan du skickar den återstående Internet trafiken till din Azure-skyddade hubb.

För Office 365 är nätverks fördröjning och prestanda avgörande för en lyckad användar upplevelse. För att uppnå dessa mål kring optimala prestanda och användar upplevelse måste kunderna implementera Office 365 Direct och Local Escape innan de överväger att vidarebefordra resten av Internet trafiken via Azure.

[Office 365 nätverks anslutnings principer](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) anrop för nyckel kontor 365 nätverks anslutningar dirigeras lokalt från användar grenen eller den mobila enheten och direkt via Internet till närmaste Microsoft-nätverksanslutning.

Dessutom är Office 365-anslutningar starkt krypterade för sekretess och använder effektiva, patentskyddade protokoll av prestanda skäl. Detta gör det opraktiskt och påverkat för att omfattas av dessa anslutningar till traditionella säkerhets lösningar på nätverks nivå. Av dessa skäl rekommenderar vi starkt att kunderna skickar Office 365-trafik direkt från grenar innan resten av trafiken skickas via Azure. Microsoft har samarbetat med flera SD-WAN-lösningar, som integrerar med Azure och Office 365 och gör det enkelt för kunder att aktivera Office 365 Direct och lokala Internet-grupp. Mer information finns i [Hur gör jag för att ange mina O365-principer via virtuellt WAN-nätverk?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Nästa steg

[Distribuera ett säkerhets partner erbjudande i en skyddad hubb med hjälp av Azure Firewall Manager](deploy-trusted-security-partner.md).
