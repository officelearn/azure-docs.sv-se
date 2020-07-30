---
title: Översikt över Azures dedikerade värdar för virtuella datorer
description: Lär dig mer om hur Azure-dedikerade värdar kan användas för att distribuera virtuella datorer.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 07/28/2020
ms.author: cynthn
ms.openlocfilehash: 97e65528a7f049c213ec077c83da1b7672469d90
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388177"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Azure-dedikerade värdar för virtuella datorer

Den dedikerade Azure-värden är en tjänst som tillhandahåller fysiska servrar som kan vara värd för en eller flera virtuella datorer som är dedikerade till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i våra data Center, som tillhandahålls som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighets zon och fel domän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som passar bäst för dina behov.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Nästa steg

- Du kan distribuera en dedikerad värd med hjälp av [Azure CLI](dedicated-hosts-cli.md), [Portal](dedicated-hosts-portal.md)och [PowerShell](../windows/dedicated-hosts-powershell.md).

- Mer information finns i Översikt över [dedikerade värdar](dedicated-hosts.md) .

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också spara pengar med en [reserverad instans av Azure-dedikerade värdar](../prepay-dedicated-hosts-reserved-instances.md).
