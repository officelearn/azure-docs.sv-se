---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0369a7792f0d9c97aa3d943708dfcc07228effa2
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330940"
---
# <a name="enabling-azure-ultra-disks"></a>Aktivera Azure ultra-diskar

Azure-diskar som ultra leverera högt dataflöde, hög IOPS och konsekvent låg latens disklagring för virtuella Azure IaaS-datorer. Det här nya erbjudandet ger upp på rad prestanda på samma tillgänglighet nivåer som våra befintliga diskar-erbjudanden. Ytterligare förmåner ultra diskar omfattar möjligheten att ändra dynamiskt prestanda för disken tillsammans med dina arbetsbelastningar utan att behöva starta om dina virtuella datorer. Ultra diskar lämpar sig för dataintensiva arbetsbelastningar som SAP HANA, översta databaser och transaktionen tunga arbetsbelastningar.

För närvarande ultra diskar finns i en förhandsversion och du måste [registrera](https://aka.ms/UltraSSDPreviewSignUp) i förhandsgranskningen för att komma åt dem.

När den godkänts, kör du något av följande kommandon för att avgöra vilken zon i östra USA 2 att distribuera ultra disken till:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

Svaret ska vara detsamma som i formuläret här nedan, där X är den zon som ska användas för att distribuera i östra USA 2. X kan vara 1, 2 eller 3.

|ResourceType  |Namn  |Plats  |Zoner  |Begränsning  |Funktion  |Värde  |
|---------|---------|---------|---------|---------|---------|---------|
|Diskar     |UltraSSD_LRS         |usaöstra2         |X         |         |         |         |

Om det fanns inget svar från kommandot, som innebär att din registrering för funktionen är fortfarande väntande eller inte godkänd ännu.

Nu när du vet vilken zon att distribuera till följer du stegen för distributionen i den här artikeln för att få din första virtuella datorer som distribueras med ultra diskar.

## <a name="deploying-an-ultra-disk"></a>Distribuera en ultra-disk

Börja med att kontrollera VM-storlek för att distribuera. En del av den här förhandsversionen är stöds endast DsV3 och EsV3 VM-familjer. Referera till den andra tabellen på den här [blogg](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) för ytterligare information om dessa storlekar på Virtuella datorer.
Även i exemplet [skapa en virtuell dator med flera ultra diskar](https://aka.ms/UltraSSDTemplate), som visar hur du skapar en virtuell dator med flera ultra diskar.

Här beskrivs nya/ändras Resource Manager ändringarna i mallen: **apiVersion** för `Microsoft.Compute/virtualMachines` och `Microsoft.Compute/Disks` måste anges som `2018-06-01` (eller senare).

Ange disken Sku UltraSSD_LRS, diskkapacitet, IOPS och dataflöde i Mbit/s för att skapa ett ultra disk. Följande är ett exempel som skapar en disk med 1 024 GiB (GiB = 2 ^ 30 byte), 80 000 IOPS och 1 200 Mbit/s (Mbit/s = 10 ^ 6 byte per sekund):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Lägga till en ytterligare funktion på egenskaperna för den virtuella datorn att ange dess ultra aktiverat (avser den [exempel](https://aka.ms/UltraSSDTemplate) för den fullständiga Resource Manager-mallen):

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

När den virtuella datorn har etablerats kan du partitionera och formatera datadiskar och konfigurera dem för dina arbetsbelastningar.

## <a name="additional-ultra-disk-scenarios"></a>Ytterligare ultra disk-scenarier

- Under skapandet av VM kan ultra diskar implicit skapas också. Men får de här diskarna ett standardvärde för IOPS (500) och dataflöde (8 MiB/s).
- Ytterligare ultra diskar kan kopplas till kompatibla virtuella datorer.
- Ultra diskar stödja justera disk Prestandaattribut (IOPS och dataflöde) vid körning utan kopplar bort disken från den virtuella datorn. När storleksändringen en disk prestanda har utfärdats på en disk, kan det ta upp till en timme för att ändringen ska börja gälla faktiskt.
- Växande diskkapaciteten kräver en virtuell dator ska frigöras.

## <a name="next-steps"></a>Nästa steg

Om du vill testa den nya disktypen och inte har registrerat dig för förhandsversionen ännu, [begära åtkomst via den här undersökningen](https://aka.ms/UltraSSDPreviewSignUp).
