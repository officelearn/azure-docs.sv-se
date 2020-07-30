---
title: Översikt över Azures dedikerade värdar för virtuella datorer
description: Lär dig mer om hur Azure-dedikerade värdar kan användas för att distribuera virtuella datorer.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 08d5f88ab018f9852da5bba5fe2230ef8148e914
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386543"
---
# <a name="azure-dedicated-hosts"></a>Dedikerade Azure-värdar

Den dedikerade Azure-värden är en tjänst som tillhandahåller fysiska servrar som kan vara värd för en eller flera virtuella datorer som är dedikerade till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i våra data Center, som tillhandahålls som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighets zon och fel domän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som passar bäst för dina behov.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera en dedikerad värd med hjälp av [Azure PowerShell](dedicated-hosts-powershell.md), [portalen](dedicated-hosts-portal.md)och [Azure CLI](../linux/dedicated-hosts-cli.md).

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också spara pengar med en [reserverad instans av Azure-dedikerade värdar](../prepay-dedicated-hosts-reserved-instances.md).
