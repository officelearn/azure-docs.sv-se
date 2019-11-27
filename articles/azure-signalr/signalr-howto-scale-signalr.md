---
title: Skala en instans av Azure SignalR service
description: Lär dig hur du skalar en Azure SignalR service-instans för att lägga till eller minska kapaciteten genom Azure Portal eller Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: zhshang
ms.openlocfilehash: 0c4f91ee9cea5e8b13ecfedafffdc1715fc242c2
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464183"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Hur skalar jag en Azure SignalR-tjänstinstans?
Den här artikeln visar hur du skalar din instans av Azure SignalR-tjänsten. Det finns två scenarier för skalning. skala upp och skala ut.

* [Skala upp](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): få fler enheter, anslutningar, meddelanden och mycket annat. Du skalar upp genom att ändra pris nivån från kostnads fri till standard.
* [Skala ut](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): öka antalet signal enheter. Du kan skala ut till så många som 100 enheter.

Det tar några minuter att tillämpa skalnings inställningarna. De kräver inte att du ändrar koden eller distribuerar om serverprogrammet.

Information om prissättning och kapacitet för enskilda signaler-tjänster finns i [pris information för Azure SignalR service](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Om du ändrar signalerar tjänsten från den **kostnads fria** nivån till **standard** -nivån eller vice versa, kommer IP-adressen för den offentliga tjänsten att ändras och det tar vanligt vis 3-60 minuter att sprida ändringen till DNS-servrar över hela Internet. Tjänsten kan vara oåtkomlig innan DNS uppdateras. Vi rekommenderar vanligt vis inte att ändra pris nivån för ofta.


## <a name="scale-on-azure-portal"></a>Skala på Azure Portal

1. Öppna [Azure-portalen](https://portal.azure.com) i webbläsaren.

2. På sidan signaler-tjänst väljer du **skala**på menyn till vänster.
   
3. Välj din pris nivå och klicka sedan på **Välj**. Du måste ange enhets antalet för **standard** nivån.
   
    ![Skala på portalen](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Klicka på **Save** (Spara).

## <a name="scale-using-azure-cli"></a>Skala med Azure CLI

Det här skriptet skapar en ny signal tjänst resurs av **kostnads fri** nivå och en ny resurs grupp, och skalar upp till **standard** nivån. 

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

## <a name="compare-pricing-tiers"></a>Jämför pris nivåer

Detaljerad information, till exempel inkluderade meddelanden och anslutningar för varje pris nivå, finns i [pris information för SignalR-tjänster](https://azure.microsoft.com/pricing/details/signalr-service/).

En tabell med tjänst gränser, kvoter och begränsningar i varje nivå finns i [begränsningar för SignalR-tjänster](../azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skalar en instans av en enskild Signals-tjänst.

Flera slut punkter stöds också för skalnings-, horisontell partitionering-och kors regions scenarier.

> [!div class="nextstepaction"]
> [skalnings signal tjänsten med flera instanser](./signalr-howto-scale-multi-instances.md)
