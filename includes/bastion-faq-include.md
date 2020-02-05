---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922e8a71a22db975685cb82bbd51a125c619ccf2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989508"
---
### <a name="regions"></a>Vilka regioner är tillgängliga?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Behöver jag en offentlig IP-adress på min virtuella dator?

När du ansluter till en virtuell dator med hjälp av Azure skydds behöver du inte en offentlig IP-adress på den virtuella Azure-dator som du ansluter till. Skydds-tjänsten öppnar RDP/SSH-sessionen/-anslutningen till den virtuella datorn via den privata IP-adressen för den virtuella datorn i det virtuella nätverket.

### <a name="is-ipv6-supported"></a>Stöds IPv6?

För tillfället stöds inte IPv6. Azure skydds stöder endast IPv4.

### <a name="rdpssh"></a>Behöver jag en RDP-eller SSH-klient?

Du behöver inte en RDP-eller SSH-klient för att få åtkomst till RDP/SSH till din virtuella Azure-dator i din Azure Portal. Använd [Azure Portal](https://portal.azure.com) så att du kan få RDP/SSH-åtkomst till den virtuella datorn direkt i webbläsaren.

### <a name="agent"></a>Behöver jag en agent som körs på den virtuella Azure-datorn?

Du behöver inte installera en agent eller någon program vara i din webbläsare eller din virtuella Azure-dator. Skydds-tjänsten är utan agent och kräver ingen ytterligare program vara för RDP/SSH.

### <a name="browsers"></a>Vilka webbläsare stöds?

Använd Microsoft Edge-webbläsaren eller Google Chrome i Windows. För Apple Mac använder du Google Chrome-webbläsaren. Microsoft Edge-krom stöds också både för Windows och Mac.

### <a name="roles"></a>Krävs det några roller för att få åtkomst till en virtuell dator?

Följande roller krävs för att upprätta en anslutning:

* Rollen läsare på den virtuella datorn
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress
* Läsar roll på Azure skydds-resursen

### <a name="pricingpage"></a>Vad är prissättningen?

Mer information finns på sidan med [priser](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Varför visas ett fel meddelande om att sessionen har upphört att gälla innan skydds-sessionen startar?

En session ska endast initieras från Azure Portal. Logga in på Azure Portal och påbörja sessionen igen. Om du går till URL: en direkt från en annan webbläsarsession eller TABB förväntas det här felet. Det hjälper till att säkerställa att sessionen är säkrare och att sessionen bara kan nås via Azure Portal.

### <a name="keyboard"></a>Vilka tangentbordslayouter stöds under skydds-fjärrsessionen?

Azure skydds stöder för närvarande en-US-QWERTY-tangentbordslayout inuti den virtuella datorn.  Stöd för andra språk för tangentbordslayout är pågående.

### <a name="udr"></a>Stöds UDR (User-Defined routing) i ett Azure skydds-undernät?

Nej. UDR stöds inte i ett Azure skydds-undernät.
För scenarier som omfattar både Azure-skydds och Azure Firewall/Network Virtual Machine (NVA) i samma virtuella nätverk behöver du inte tvinga trafik från ett Azure skydds-undernät till Azure-brandväggen eftersom kommunikationen mellan Azure skydds och dina virtuella datorer är privat. Mer information finns i [komma åt virtuella datorer bakom Azure Firewall med skydds](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Stöds fil överföring med Azure skydds RDP-sessionen?

Vi arbetar hårt för att lägga till nya funktioner. Från och med nu stöds inte fil överföring, men är en del av vår översikt. Du kan gärna dela din feedback om nya funktioner på feedback- [sidan för Azure skydds](https://feedback.azure.com/forums/217313-networking?category_id=367303).
