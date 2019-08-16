---
title: Anslut Azure Virtual Network till CloudSimple med ExpressRoute
description: Beskriver hur du hämtar peering-information för en anslutning mellan det virtuella Azure-nätverket och din CloudSimple-miljö
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536604"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Anslut Azure Virtual Network till CloudSimple med ExpressRoute

Du kan utöka ditt privata moln nätverk till ditt virtuella Azure-nätverk och Azure-resurser. Med en ExpressRoute-anslutning kan du komma åt resurser som körs i din Azure-prenumeration från ditt privata moln.

## <a name="request-authorization-key"></a>Verifierings nyckel för begäran

Det krävs en auktoriseringspost för ExpressRoute-anslutningen mellan ditt privata moln och det virtuella Azure-nätverket. För att hämta en nyckel, File a Ticket with <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">support</a>.  Använd följande information i begäran:

* Typ av problem: **Produkt**
* Prenumeration: Välj den prenumeration där CloudSimple-tjänsten har distribuerats * *
* Tjänst: **VMware-lösning av CloudSimple**
* Problem typ: **Tjänstbegäran**
* Problem under typ: **Auktoriseringsregel för Azure VNET-anslutning**
* Ämne: **Begäran om auktoriseringskod för Azure VNET-anslutning**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Hämta peering-information för Azure Virtual Network till CloudSimple-anslutning

Om du vill konfigurera anslutningen måste du upprätta en länk mellan Azure Virtual Network och din CloudSimple-miljö.  Som en del av proceduren måste du ange peer-kretsens URI och verifierings nyckel. Hämta URI och verifierings nyckel från [CloudSimple-portalen](access-cloudsimple-portal.md).  Välj **nätverk** på sido menyn och välj sedan **Azure nätverks anslutning**. Eller Välj **konto** på sido menyn och välj sedan **Azure-nätverksanslutning**.

Observera kopierings ikonerna för peer-krets-URI och för verifierings nyckeln för varje region. För varje privat moln som du vill ansluta:

* Kopiera URI: n genom att klicka på **Kopiera** . Klistra in den i en fil där den kan vara tillgänglig att läggas till i Azure Portal.  
* Klicka på **Kopiera** för att kopiera verifierings nyckeln och klistra in den i filen även.

![Sidan Virtual Network anslutning](media/network-virt-conn-page.png)

Mer information om hur du konfigurerar det virtuella Azure-nätverket till CloudSimple-länken finns i [ansluta din CloudSimple-privata moln miljö till det virtuella Azure-nätverket med hjälp av ExpressRoute](azure-expressroute-connection.md).
