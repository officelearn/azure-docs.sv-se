---
title: Anslut Azure Virtual Network till AVS med ExpressRoute
description: Beskriver hur du hämtar peering-information för en anslutning mellan det virtuella Azure-nätverket och din AVS-miljö
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014410"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Anslut Azure Virtual Network till AVS med ExpressRoute

Du kan utöka ditt moln nätverk i molnet till ditt virtuella Azure-nätverk och Azure-resurser. Med en ExpressRoute-anslutning kan du komma åt resurser som körs i din Azure-prenumeration från ditt molns privata moln.

## <a name="request-authorization-key"></a>Verifierings nyckel för begäran

Det krävs en auktoriseringspost för ExpressRoute-anslutningen mellan ditt moln-eller Azure-moln och det virtuella Azure-nätverket. För att hämta en nyckel, File a Ticket with <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">support</a>. Använd följande information i begäran:

* Typ av problem: **teknisk**
* Prenumeration: **Välj den prenumeration där AVS-tjänsten distribueras**
* Tjänst: **VMware-lösningar (AVS)**
* Problem typ: **tjänstbegäran**
* Problem under typ: **auktoriseringsregel för Azure VNET-anslutning**
* Subject: **begäran om auktoriseringskod för Azure VNET-anslutning**

## <a name="get-peering-information-from-avs-portal"></a>Hämta peering-information från AVS-Portal

Om du vill konfigurera anslutningen måste du upprätta en anslutning mellan Azure Virtual Network och din AVS-miljö. Som en del av proceduren måste du ange peer-kretsens URI och verifierings nyckel. Hämta URI och verifierings nyckel från [AVS-portalen](access-cloudsimple-portal.md). Välj **nätverk** på sido menyn och välj sedan **Azure nätverks anslutning**. Eller Välj **konto** på sido menyn och välj sedan **Azure-nätverksanslutning**.

Kopiera peer-krets-URI och för verifierings nyckeln för var och en av de regioner som använder *Kopiera* -ikonen. För varje AVS-region som du vill ansluta:

1. Kopiera URI: n genom att klicka på **Kopiera** . Klistra in den i en fil där den kan vara tillgänglig att läggas till i Azure Portal. 
2. Klicka på **Kopiera** för att kopiera verifierings nyckeln och klistra in den i filen även.

Kopiera verifierings nyckeln och den peer-krets-URI som är i tillståndet **tillgängligt** . **Använd** status anger att nyckeln redan har använts för att skapa en virtuell nätverks anslutning.

![Sidan Virtual Network anslutning](media/virtual-network-connection.png)

Mer information om hur du konfigurerar det virtuella Azure-nätverket till en AVS-länk finns i [ansluta din AVS-miljö med privata moln till det virtuella Azure-nätverket med ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Network-anslutning till ett privat AVS-moln](azure-expressroute-connection.md)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](on-premises-connection.md)
