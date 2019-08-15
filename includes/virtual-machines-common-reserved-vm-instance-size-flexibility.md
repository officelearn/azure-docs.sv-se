---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: a98a941477fc83a104b55ed91f457c5a48f90d59
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029872"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Storleks flexibilitet för virtuella datorer med reserverade VM-instanser

Med en reserverad virtuell dator instans som är optimerad för flexibilitet i instans storleken kan den reservation du köper tillämpa på storlekarna för virtuella datorer (VM) i samma instans storlek flexibilitet grupp. Om du till exempel köper en reservation för en VM-storlek som anges i DSv2-serien, t. ex. Standard_DS5_v2, kan reservations rabatten gälla för de andra fyra storlekarna som anges i samma instans storlek flexibilitet grupp:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Men reservations rabatten gäller inte för VM-storlekar som listas i olika storleks flexibla grupper, t. ex. SKU: er i DSv2-seriens höga minne: Standard_DS11_v2, Standard_DS12_v2 och så vidare.

I flexibilitets gruppen för instans storlek gäller antalet virtuella datorer som reservations rabatten gäller beroende på storleken på den virtuella dator som du väljer när du köper en reservation. Det beror också på storleken på de virtuella datorer som du kör. I kolumnen förhållande jämförs de relativa utrymmena för varje VM-storlek i den instans storlekens flexibilitet grupp. Använd ratio-värdet för att beräkna hur reservations rabatten gäller för de virtuella datorer som du kör.

## <a name="examples"></a>Exempel

I följande exempel används storlekarna och förhållandena i DSv2-seriens tabell.

Du köper en reserverad VM-instans med storleken Standard_DS4_v2 där kvoten eller den relativa storleken jämfört med de andra storlekarna i serien är 8.

- Scenario 1: Kör åtta virtuella datorer med Standard_DS1_v2 storlek med förhållandet 1. Reservations rabatten gäller för alla åtta av de virtuella datorerna.
- Scenario 2: Kör två Standard_DS2_v2 storleks datorer med förhållandet 2 var. Kör även en virtuell dator med Standard_DS3_v2 storlek med förhållandet 4. Det totala utrymmet är 2 + 2 + 4 = 8. Reservations rabatten gäller för alla tre virtuella datorer.
- Scenario 3: Kör en Standard_DS5_v2 med förhållandet 16. Reservations rabatten gäller för hälften av den virtuella datorns beräknings kostnad.

I följande avsnitt visas vilka storlekar som finns i samma storleks serie grupp när du köper en reserverad VM-instans som är optimerad för storleks flexibilitet i en instans.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Flexibilitet för instans storlek för virtuella datorer 

CSV nedan har grupper som är flexibla för instans storlek, ArmSkuName och förhållandet.  

[Flexibilitet för instans storlek](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Vi behåller fil-URL: en och schemat åtgärdat så att du kan använda den här filen program mässigt. Data kommer också att vara tillgängliga via API snart.
