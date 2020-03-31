---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471471"
---
När du köper en reserverad VM-instans kan du välja att optimera till exempel storleksflexibilitet eller kapacitetsprioritet. Mer information om hur du ställer in eller ändrar optimeringsinställningen för reserverade VM-instanser finns i [Ändra optimeringsinställningen för reserverade VM-instanser](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Med en reserverad instans för virtuella datorer som är optimerad för instansstorleksflexibilitet kan den reservation du köper gälla för storleken på virtuella datorer (VMs) i samma flexibilitetsgrupp för instansstorlek. Om du till exempel köper en reservation för en VM-storlek som visas i DSv2-serien, till exempel Standard_DS5_v2, kan reservationsrabatten gälla för de andra fyra storlekarna som visas i samma flexibilitetsgrupp för förekomststorlek:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Men den reservationsrabatten gäller inte för virtuella datorer storlekar som visas i olika instansstorleksflexibilitetsgrupper, till exempel SKU:er i DSv2-seriens höga minne: Standard_DS11_v2, Standard_DS12_v2 och så vidare.

Inom gruppen för instansstorleksflexibilitet beror antalet virtuella datorer som reservationsrabatten gäller för på vilken vm-storlek du väljer när du köper en reservation. Det beror också på storleken på de virtuella datorer som du har igång. Ratiokolumnen jämför det relativa fotavtrycket för varje vm-storlek i den flexibilitetsgruppen för instansstorlek. Använd kvotvärdet för att beräkna hur reservationsrabatten gäller för de virtuella datorer som du har kört.

## <a name="examples"></a>Exempel

I följande exempel används storlekarna och proportionerna i tabellen DSv2-serien.

Du köper en reserverad VM-instans med storleken Standard_DS4_v2 där förhållandet eller det relativa fotavtrycket jämfört med de andra storlekarna i den serien är 8.

- Scenario 1: Kör åtta Standard_DS1_v2 medelstora virtuella datorer med ett förhållande på 1. Din bokningsrabatt gäller för alla åtta av dessa virtuella datorer.
- Scenario 2: Kör två Standard_DS2_v2 medelstora virtuella datorer med ett förhållande på 2 vardera. Kör också en Standard_DS3_v2 storlek VM med ett förhållande på 4. Det totala fotavtrycket är 2+2+4=8. Så din bokning rabatt gäller för alla tre av dessa virtuella datorer.
- Scenario 3: Kör en Standard_DS5_v2 med ett förhållande på 16. Din reservationsrabatt gäller för hälften av den virtuella datorns beräkningskostnad.

Följande avsnitt visar vilka storlekar som finns i samma storleksseriegrupp när du köper en reserverad VM-instans optimerad till exempel storleksflexibilitet.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Flexibilitetsförhållande för instansstorlek för virtuella datorer 

CSV nedan har instansstorlek flexibilitet grupper, ArmSkuName och nyckeltal.  

[Flexibilitetsförhållanden för instansstorlek](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Vi kommer att hålla filens URL och schemat fast så att du kan använda den här filen programmässigt. Uppgifterna kommer också att vara tillgängliga via API snart.
