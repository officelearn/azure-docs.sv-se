---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57a764b62fcda333f042794e176c24c8e6cc5526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80374066"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Vilka regioner är tillgängliga?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Behöver jag en offentlig IP på min virtuella dator?

När du ansluter till en virtuell dator med Azure Bastion behöver du INTE en offentlig IP på den virtuella Azure-datorn som du ansluter till. Bastion-tjänsten öppnar RDP/SSH-sessionen/anslutningen till din virtuella dator via den privata IP-adressen för din virtuella dator, i ditt virtuella nätverk.

### <a name="is-ipv6-supported"></a>Stöds IPv6?

För närvarande stöds inte IPv6. Azure Bastion stöder endast IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Behöver jag en RDP- eller SSH-klient?

Du behöver inte en RDP- eller SSH-klient för att komma åt RDP/SSH till din virtuella Azure-dator i din Azure-portal. Använd [Azure-portalen](https://portal.azure.com) för att du ska kunna få RDP/SSH-åtkomst till din virtuella dator direkt i webbläsaren.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Kräver Azure Bastion en RDS CAL för administrativa ändamål på Azure-värd virtuella datorer?
Nej, åtkomst till virtuella Windows Server-datorer från Azure Bastion kräver inte en [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) när den används enbart för administrativa ändamål.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hur många samtidiga RDP- och SSH-sessioner stöder varje Azure Bastion?
Både RDP och SSH är ett användningsbaserat protokoll. Hög användning av sessioner gör att skyddsvärden stöder ett lägre totalt antal sessioner. Siffrorna nedan förutsätter normala dagliga arbetsflöden.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Behöver jag en agent som körs i den virtuella Azure-datorn?

Du behöver inte installera en agent eller någon programvara i din webbläsare eller din virtuella Azure-dator. Bastion-tjänsten är agentlös och kräver ingen ytterligare programvara för RDP/SSH.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Vilka webbläsare stöds?

Använd webbläsaren Microsoft Edge eller Google Chrome i Windows. Använd webbläsaren Google Chrome för Apple Mac. Microsoft Edge Chromium stöds också på både Windows och Mac, respektive.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Krävs några roller för att komma åt en virtuell dator?

För att kunna ansluta krävs följande roller:

* Läsarroll på den virtuella datorn
* Läsarroll på nätverkskortet med den virtuella datorns privata IP
* Läsarrollen på Azure Bastion-resursen

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Vad är prissättningen?

Mer information finns på sidan med [priser](https://aka.ms/BastionHostPricing).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Varför får jag felmeddelandet "Din session har gått ut" innan Bastion-sessionen startar?

En session bör endast initieras från Azure-portalen. Logga in på Azure-portalen och börja din session igen. Om du går till webbadressen direkt från en annan webbläsarsession eller flik förväntas det här felet. Det hjälper till att säkerställa att din session är säkrare och att sessionen endast kan nås via Azure-portalen.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Vilka tangentbordslayouter stöds under fjärrsessionen Bastion?

Azure Bastion stöder för närvarande tangentbordslayout för en-us-qwerty inuti den virtuella datorn.  Stöd för andra språk för tangentbordslayout pågår.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Stöds användardefinierad routning (UDR) i ett Azure Bastion-undernät?

Nej. UDR stöds inte på ett Azure Bastion-undernät.
För scenarier som innehåller både Azure Bastion och Azure Firewall/Network Virtual Appliance (NVA) i samma virtuella nätverk behöver du inte tvinga trafik från ett Azure Bastion-undernät till Azure-brandväggen eftersom kommunikationen mellan Azure Bastion och dina virtuella datorer är privat. Mer information finns i [Komma åt virtuella datorer bakom Azure-brandväggen med Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="is-file-transfer-supported-with-azure-bastion-rdp-session"></a><a name="filetransfer"></a>Stöds filöverföring med Azure Bastion RDP-session?

Vi arbetar hårt för att lägga till nya funktioner. Från och med nu stöds inte filöverföring, men är en del av vår färdplan. Du får gärna dela med dig av din feedback om nya funktioner på [sidan Azure Bastion Feedback](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hur hanterar jag distributionsfel?

Granska eventuella felmeddelanden och [ta upp en supportbegäran i Azure Portal](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) efter behov. Distributionsfel kan bero på [Azure-prenumerationsbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Specifikt kan kunder stöta på en gräns för antalet offentliga IP-adresser som tillåts per prenumeration som gör att Azure Bastion-distributionen misslyckas.
