---
title: Översikt över Azure Dedikerada värdar för virtuella datorer
description: Läs mer om hur Azure Dedikerade värdar kan användas för att distribuera virtuella datorer.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970730"
---
# <a name="azure-dedicated-hosts"></a>Azure dedikerade värdar

Azure Dedicated Host är en tjänst som tillhandahåller fysiska servrar – som kan vara värd för en eller flera virtuella datorer – som är dedikerad till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i våra datacenter, som tillhandahålls som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighetszon och feldomän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som bäst uppfyller dina behov.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Nästa steg

- Du kan distribuera en dedikerad värd med [Azure CLI,](dedicated-hosts-cli.md) [portal](dedicated-hosts-portal.md)och [PowerShell](../windows/dedicated-hosts-powershell.md).

- Mer information finns i översikten [Dedikerade värdar.](dedicated-hosts.md)

- Det finns exempelmall, som finns [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och feldomäner för maximal återhämtning i en region.

- Du kan också spara på kostnader med en [reserverad instans av Azure Dedikerad värdar](../prepay-dedicated-hosts-reserved-instances.md).
