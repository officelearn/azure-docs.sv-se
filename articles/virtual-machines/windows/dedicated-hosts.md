---
title: Översikt över Azure Dedikerada värdar för virtuella datorer
description: Läs mer om hur Azure Dedikerade värdar kan användas för att distribuera virtuella datorer.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251554"
---
# <a name="azure-dedicated-hosts"></a>Azure dedikerade värdar

Azure Dedicated Host är en tjänst som tillhandahåller fysiska servrar – som kan vara värd för en eller flera virtuella datorer – som är dedikerad till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i våra datacenter, som tillhandahålls som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighetszon och feldomän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som bäst uppfyller dina behov.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera en dedikerad värd med [Azure PowerShell,](dedicated-hosts-powershell.md) [portalen](dedicated-hosts-portal.md)och [Azure CLI](../linux/dedicated-hosts-cli.md).

- Det finns exempelmall, som finns [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och feldomäner för maximal återhämtning i en region.

- Du kan också spara på kostnader med en [reserverad instans av Azure Dedikerad värdar](../prepay-dedicated-hosts-reserved-instances.md).
