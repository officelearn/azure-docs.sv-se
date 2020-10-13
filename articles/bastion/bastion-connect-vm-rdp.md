---
title: Ansluta till en virtuell Windows-dator med Azure skydds
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Azure-dator som kör Windows med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978155"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ansluta till en virtuell Windows-dator med Azure skydds

Med hjälp av Azure skydds kan du på ett säkert och smidigt sätt ansluta till dina virtuella datorer via SSL direkt i Azure Portal. När du använder Azure skydds kräver inte de virtuella datorerna någon klient, agent eller ytterligare program vara. Den här artikeln visar hur du ansluter till dina virtuella Windows-datorer. Information om hur du ansluter till en virtuell Linux-dator finns i [ansluta till en virtuell dator med Azure skydds – Linux](bastion-connect-vm-ssh.md).

Azure skydds ger säker anslutning till alla virtuella datorer i det virtuella nätverk där det är etablerad. Med hjälp av Azure skydds kan du skydda dina virtuella datorer från att exponera RDP/SSH-portar till utsidan, samtidigt som du ger säker åtkomst med RDP/SSH. Mer information finns i [Översikt](bastion-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="install-the-bastion-host"></a>Installera skydds-värden

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det virtuella nätverket. Information om hur du konfigurerar en Azure skydds-värd finns i [skapa en Azure skydds-värd](bastion-create-host-portal.md).

### <a name="required-roles"></a>Nödvändiga roller

Följande roller krävs för att upprätta en anslutning:

* Rollen läsare på den virtuella datorn
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress
* Läsar roll på Azure skydds-resursen

### <a name="ports"></a>Portar

För att ansluta till den virtuella Windows-datorn måste du ha följande portar öppna på din virtuella Windows-dator:

* Inkommande portar: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Anslut

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
