---
title: Använda portalen för att distribuera virtuella Azure-datorer
description: Använda Azure PowerShell för att distribuera virtuella datorer för virtuella datorer för att spara pengar.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0da650646c35a9a663dd29589f963d23cbe552cf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828612"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Distribuera virtuella datorer med hjälp av Azure Portal

Med hjälp av [virtuella datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande besparingar. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella datorer. De virtuella datorerna är därför fantastiska för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för virtuella datorer i virtuella datorer är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du ställer in högsta pris finns i [VM-priser för virtuella datorer](spot-vms.md#pricing).

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en VM-VM kan anges i USD (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.05701` vara ett max pris på $0,05701 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.

När den virtuella datorn avlägsnas har du möjlighet att antingen ta bort den virtuella datorn och den underliggande disken eller frigöra den virtuella datorn så att den kan startas om senare.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

När du distribuerar en virtuell dator kan du välja att använda en Azure-tjänstinstans.


På fliken **grundläggande** i avsnittet **instans information** är **Nej** standard för användning av en Azure-tjänstinstans.

![Skärm bild där du väljer Nej, Använd inte en Azure-tjänstinstans](./media/spot-portal/no.png)

Om du väljer **Ja**expanderas avsnittet och du kan välja en princip för [borttagning och borttagning](spot-vms.md#eviction-policy). 

![Skärm bild för att välja Ja, Använd en Azure-tjänstinstans](./media/spot-portal/yes.png)

Du kan också jämföra priser och avlägsna priser med andra liknande regioner genom att välja **Visa pris historik och jämföra priser i närliggande regioner**.

I det här exemplet är den centrala Kanada-regionen billigare och har en lägre avtagnings hastighet än regionen USA, östra.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Skärm bild av alternativen för region med skillnaden i pris-och borttagnings frekvens.":::

Du kan ändra region genom att välja det alternativ som passar dig bäst och sedan välja **OK**.

## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan [simulera en avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) av en virtuell dator för att testa hur bra ditt program kommer att återdamma till en plötslig avlägsning. 

Ersätt följande med din information: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa virtuella dekor maskiner med [PowerShell](./windows/spot-powershell.md), [CLI](./linux/spot-cli.md)eller en [mall](./linux/spot-template.md).
