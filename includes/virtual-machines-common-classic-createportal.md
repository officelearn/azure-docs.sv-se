---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 127ade3b37380c50461c5b648265b431a2f4467e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227057"
---
Med en *anpassad* virtuell dator avses en virtuell dator som du skapar med en **aktuell app** från **Marketplace** eftersom den gör en stor del av arbetet åt dig. Men du kan fortfarande välja olika konfigurationsalternativ:

* Ansluta den virtuella datorn till ett virtuellt nätverk.
* Installera Azure Virtual Machine-agenten och Azure Virtual Machine-tillägg, till exempel för programvara mot skadlig kod.
* Lägga till den virtuella datorn till befintliga molntjänster.
* Lägga till den virtuella datorn i ett befintligt lagringskonto.
* Lägga till den virtuella datorn i en tillgänglighetsuppsättning.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Om du vill att den virtuella datorn ska använda ett virtuellt nätverk måste du ange det virtuella nätverket när du skapar den virtuella datorn.

> * Två fördelar med att använda ett virtuellt nätverk är att du kan ansluta direkt till den virtuella datorn och konfigurera anslutningar mellan platser.

> * En virtuell dator kan endast konfigureras för att ansluta till ett virtuellt nätverk när du skapar den virtuella datorn. Mer information om virtuella nätverk finns i [Översikt över Azure Virtual Network](../articles/virtual-network/virtual-networks-overview.md).
>
>

## <a name="to-create-the-virtual-machine"></a>Skapa en virtuell dator
