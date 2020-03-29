---
title: Skala en instans av Azure SignalR-tjänsten
description: Lär dig hur du skalar en Azure SignalR-tjänstinstans för att lägga till eller minska kapaciteten, via Azure Portal eller Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659295"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Hur skalar du en Azure SignalR-tjänstinstans?
Den här artikeln visar hur du skalar din instans av Azure SignalR-tjänsten. Det finns två scenarier för skalning, skala upp och skala ut.

* [Skala upp](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Få fler enheter, anslutningar, meddelanden med mera. Du skalar upp genom att ändra prisnivån från gratis till standard.
* [Skala ut](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Öka antalet SignalR-enheter. Du kan skala ut till så många som 100 enheter.

Skalningsinställningarna tar några minuter att tillämpa. I sällsynta fall kan det ta cirka 30 minuter att applicera. De kräver inte att du ändrar koden eller distribuerar om serverprogrammet.

Information om priser och kapacitet för enskilda SignalR-tjänster finns i [Azure SignalR Service Pricing Details](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Ändra SignalR-tjänst från **fri** nivå till **standardnivå** eller vice versa, public service IP kommer att ändras och det tar vanligtvis 30-60 minuter att sprida ändringen till DNS-servrar över hela Internet. Tjänsten kan inte nås innan DNS uppdateras. I allmänhet rekommenderas det inte att ändra din prisnivå för ofta.


## <a name="scale-on-azure-portal"></a>Skala på Azure-portalen

1. Öppna [Azure-portalen](https://portal.azure.com) i webbläsaren.

2. Välj **Skala**på sidan SignalR-tjänst på sidan SignalR-tjänst .
   
3. Välj prisnivå och klicka sedan på **Välj**. Ange enhetsantalet för **standardnivå.**
   
    ![Skala på portalen](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Klicka på **Spara**.

## <a name="scale-using-azure-cli"></a>Skala med Azure CLI

Det här skriptet skapar en ny SignalR-tjänstresurs **för kostnadsfri** nivå och en ny resursgrupp och skalar den upp till **standardnivå.** 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Anteckna det faktiska namnet som genereras för den nya resursgruppen. Du använder resursgruppens namn när du vill ta bort alla gruppresurser.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Jämför prisnivåer

Detaljerad information, till exempel inkluderade meddelanden och anslutningar för varje prisnivå, finns i [Information om prissättning av SignalR-tjänst.](https://azure.microsoft.com/pricing/details/signalr-service/)

En tabell över tjänstbegränsningar, kvoter och begränsningar på varje nivå finns i [Gränser för SignalR-tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skalar en enda SignalR-tjänstinstans.

Flera slutpunkter stöds också för skalning, fragmentering och scenarier mellan regioner.

> [!div class="nextstepaction"]
> [skala SignalR-tjänsten med flera instanser](./signalr-howto-scale-multi-instances.md)
