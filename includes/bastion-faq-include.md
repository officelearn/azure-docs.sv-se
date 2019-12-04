---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ada64d6bbce4fe7401bbfc078ba0903a28f06971
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795622"
---
### <a name="regions"></a>Vilka regioner är tillgängliga?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Behöver jag en offentlig IP-adress på min virtuella dator?

Du behöver inte en offentlig IP-adress på den virtuella Azure-dator som du ansluter till med Azure skydds-tjänsten. Skydds-tjänsten öppnar RDP/SSH-sessionen/-anslutningen till den virtuella datorn via den privata IP-adressen för den virtuella datorn i det virtuella nätverket.

### <a name="rdpssh"></a>Behöver jag en RDP-eller SSH-klient?

Du behöver ingen RDP- eller SSH-klient för att få åtkomst till RDP/SSH till din virtuella Azure-dator i din Azure-portalen. Använd [Azure Portal](https://portal.azure.com) så att du kan få RDP/SSH-åtkomst till den virtuella datorn direkt i webbläsaren.

### <a name="agent"></a>Behöver jag en agent som körs på den virtuella Azure-datorn?

Du behöver inte installera någon agent eller programvara i webbläsaren eller den virtuella Azure-datorn. Tjänsten Bastion är agentlös och ingen ytterligare programvara krävs för RDP/SSH.

### <a name="browsers"></a>Vilka webbläsare stöds?

Använd Microsoft Edge-webbläsaren eller Google Chrome i Windows. För Apple Mac använder du webbläsaren Google Chrome. Microsoft Edge Chromium kan också användas på både Windows och Mac.

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
