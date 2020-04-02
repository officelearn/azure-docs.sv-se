---
title: Använda portalen för att distribuera virtuella Azure Spot-datorer
description: Lär dig hur du använder Azure PowerShell för att distribuera spot-datorer för att spara på kostnader.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547388"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Distribuera spot-datorer med Azure-portalen

Med hjälp av [Spot virtuella datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till en betydande kostnadsbesparingar. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort spot-datorer. Därför är spot-virtuella datorer bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Priserna för spot-virtuella datorer varierar, baserat på region och SKU. Mer information finns i VM-priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du anger maxpriset finns i [Spot-virtuella datorer - Prissättning](spot-vms.md#pricing).

Du har möjlighet att ställa in ett maxpris som du är villig att betala, per timme, för den virtuella datorn. Maxpriset för en spot-VM kan ställas in i US-dollar (USD), med upp till 5 decimaler. Värdet `0.05701`skulle till exempel vara ett maxpris på 0,05701 USD per timme. Om du ställer in `-1`maxpriset så kommer den virtuella datorn inte att vräkas baserat på priset. Priset för den virtuella datorn blir det aktuella priset för avista eller priset för en vanlig virtuell dator, som någonsin är mindre, så länge det finns kapacitet och kvot tillgänglig.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Processen för att skapa en virtuell dator som använder spot-datorer är densamma som beskrivs i [snabbstarten](quick-create-portal.md). När du distribuerar en virtuell dator kan du välja att använda en Azure-platsinstans.


På fliken **Grunderna** i avsnittet **Instansinformation** är **Nej** standard för att använda en Azure-spotinstans.

![Skärminfångst för att välja nej, använd inte en Azure-platsinstans](media/spot-portal/no.png)

Det du väljer **Ja,** avsnittet expanderar och du kan välja din [vräkningstyp och vräkningsprincip](spot-vms.md#eviction-policy). 

![Skärminfångst för att välja ja, använd en Azure-platsinstans](media/spot-portal/yes.png)


## <a name="next-steps"></a>Nästa steg

Du kan också skapa spot-datorer med [PowerShell](spot-powershell.md).