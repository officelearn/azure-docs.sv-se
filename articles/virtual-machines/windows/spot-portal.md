---
title: Använda portalen för att distribuera virtuella Azure-datorer
description: Lär dig hur du använder Azure PowerShell för att distribuera virtuella datorer för virtuella datorer för att spara pengar.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158986"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>För hands version: distribuera virtuella datorer med hjälp av Azure Portal

Med hjälp av [virtuella datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande besparingar. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella datorer. De virtuella datorerna är därför fantastiska för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för virtuella datorer i virtuella datorer är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du ställer in högsta pris finns i [VM-priser för virtuella datorer](spot-vms.md#pricing).

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en VM-VM kan anges i USD (USD) med upp till 5 decimaler. Värdet `0.05701`skulle till exempel vara ett max pris på $0,05701 USD per timme. Om du anger det högsta priset som ska `-1`avlägsnas inte den virtuella datorn baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.

> [!IMPORTANT]
> Punkt instanser finns för närvarande i offentlig för hands version.
> Den här för hands versionen rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Processen för att skapa en virtuell dator som använder virtuella datorer är samma som i [snabb](quick-create-portal.md)starten. När du distribuerar en virtuell dator kan du välja att använda en Azure-tjänstinstans.


På fliken **grundläggande** i avsnittet **instans information** är **Nej** standard för användning av en Azure-tjänstinstans.

![Skärm bild där du väljer Nej, Använd inte en Azure-tjänstinstans](media/spot-portal/no.png)

Du väljer **Ja**, avsnittet expanderas och du kan välja en princip för [borttagning och borttagning](spot-vms.md#eviction-policy). 

![Skärm bild för att välja Ja, Använd en Azure-tjänstinstans](media/spot-portal/yes.png)


## <a name="next-steps"></a>Nästa steg

Du kan också skapa virtuella dekor maskiner med [PowerShell](spot-powershell.md).