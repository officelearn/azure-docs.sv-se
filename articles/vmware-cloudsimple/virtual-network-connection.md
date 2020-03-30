---
title: Anslut virtuella Azure-nätverk till CloudSimple med ExpressRoute - Azure VMware-lösning från CloudSimple
description: Beskriver hur du hämtar peering-information för en anslutning mellan det virtuella Azure-nätverket och cloudsimple-miljön
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563985"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Ansluta virtuella Azure-nätverk till CloudSimple med ExpressRoute

Du kan utöka ditt Private Cloud-nätverk till ditt virtuella Azure-nätverk och Azure-resurser. Med en ExpressRoute-anslutning kan du komma åt resurser som körs i din Azure-prenumeration från ditt privata moln.

## <a name="request-authorization-key"></a>Begär auktoriseringsnyckel

En auktoriseringsnyckel krävs för ExpressRoute-anslutningen mellan ditt privata moln och det virtuella Azure-nätverket. Om du vill hämta en nyckel lämnar du in en biljett till <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Support</a>.  Använd följande information i begäran:

* Typ av problem: **Teknisk**
* Prenumeration: **Välj den prenumeration där CloudSimple-tjänsten distribueras**
* Tjänst: **VMware-lösning från CloudSimple**
* Problemtyp: **Servicebegäran**
* Problemundertyp: **Auktoriseringsnyckel för Azure VNET-anslutning**
* Ämne: **Begäran om auktoriseringsnyckel för Azure VNET-anslutning**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Hämta peering-information från CloudSimple-portalen

För att konfigurera anslutningen måste du upprätta en anslutning mellan virtuellt Azure-nätverk och din CloudSimple-miljö.  Som en del av proceduren måste du ange peer circuit URI och auktoriseringsnyckel. Hämta URI- och auktoriseringsnyckeln från [CloudSimple-portalen](access-cloudsimple-portal.md).  Välj **Nätverk** på sidomenyn och välj sedan **Azure Network Connection**. Eller välj **Konto** på sidomenyn och välj sedan **Azure-nätverksanslutning**.

Kopiera peer circuit URI och för auktoriseringsnyckeln för var och en av regionerna med *hjälp* av kopieringsikonen. För varje CloudSimple-region som du vill ansluta:

1. Klicka på **Kopiera** om du vill kopiera URI:n. Klistra in den i en fil där den kan vara tillgänglig för att lägga till i Azure-portalen.  
2. Klicka på **Kopiera** om du vill kopiera auktoriseringsnyckeln och klistra in den i filen.

Kopiera auktoriseringsnyckeln och peer circuit-URI:n som är i **tillgängligt** tillstånd.  **Använd** status anger att nyckeln redan har använts för att skapa en virtuell nätverksanslutning.

![Sidan Anslutning till virtuellt nätverk](media/virtual-network-connection.png)

Mer information om hur du konfigurerar azure-nätverket till CloudSimple-länken finns i [Ansluta din CloudSimple Private Cloud-miljö till det virtuella Azure-nätverket med ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Nästa steg

* [Virtuell azure-nätverksanslutning till Privat moln](azure-expressroute-connection.md)
* [Ansluta till lokalt nätverk med Azure ExpressRoute](on-premises-connection.md)
