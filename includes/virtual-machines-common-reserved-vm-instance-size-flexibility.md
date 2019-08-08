---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857502"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Storleks flexibilitet för virtuella datorer med reserverade VM-instanser

Med en reserverad virtuell dator instans som är optimerad för flexibilitet i instans storleken kan den reservation du köper tillämpa på de virtuella datorernas (VM) storlekar i samma storleks serie grupp. Om du till exempel köper en reservation för en VM-storlek som anges i tabellen DSv2-serien, till exempel Standard_DS5_v2, kan reservations rabatten gälla för de andra fyra storlekarna som anges i samma tabell:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Men reservations rabatten gäller inte för VM-storlekar som listas i olika tabeller, t. ex. vad som finns i tabellen med hög minnes DSv2-serien: Standard_DS11_v2, Standard_DS12_v2 och så vidare.

I storleks serie gruppen gäller antalet virtuella datorer som reservations rabatten gäller beroende på storleken på den virtuella dator som du väljer när du köper en reservation. Det beror också på storleken på de virtuella datorer som du kör. Relations kolumnen som visas i följande tabeller jämför det relativa utrymmet för varje VM-storlek i gruppen. Använd ratio-värdet för att beräkna hur reservations rabatten gäller för de virtuella datorer som du kör.

## <a name="examples"></a>Exempel

I följande exempel används storlekarna och förhållandena i DSv2-seriens tabell.

 Du köper en reserverad VM-instans med storleken Standard_DS4_v2 där kvoten eller den relativa storleken jämfört med de andra storlekarna i serien är 8.

- Scenario 1: Kör åtta virtuella datorer med Standard_DS1_v2 storlek med förhållandet 1. Reservations rabatten gäller för alla åtta av de virtuella datorerna.
- Scenario 2: Kör två Standard_DS2_v2 storleks datorer med förhållandet 2 var. Kör även en virtuell dator med Standard_DS3_v2 storlek med förhållandet 4. Det totala utrymmet är 2 + 2 + 4 = 8. Reservations rabatten gäller för alla tre virtuella datorer.
- Scenario 3: Kör en Standard_DS5_v2 med förhållandet 16. Reservations rabatten gäller för hälften av den virtuella datorns beräknings kostnad.

I följande avsnitt visas vilka storlekar som finns i samma storleks serie grupp när du köper en reserverad VM-instans som är optimerad för storleks flexibilitet i en instans.

## <a name="b-series"></a>B-serien

| Size | Faktor|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Mer information finns i storlekarna för [burst-storlek för virtuella datorer i B-serien](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Hög minne i B-serien

| Size | Faktor|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Mer information finns i storlekarna för [burst-storlek för virtuella datorer i B-serien](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-serien

| Size | Faktor|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Hög minne i D-serien

| Size | Faktor|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>DS-serien

| Size | Faktor|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Active Directory-seriens höga minne

| Size | Faktor|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-serien

| Size | Faktor|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>DSv2-seriens höga minne

| Size | Faktor|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>DSv2 med hög minne isolerad-serien

| Size | Faktor|
|---|---|
|Standard_DS15i_v2|1|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>DSv3-serien

| Size | Faktor|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Mer information finns i [generell användnings storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Dv2-serien

| Size | Faktor|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Dv2-seriens höga minne

| Size | Faktor|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Dv2 med hög minne isolerad-serien

| Size | Faktor|
|---|---|
|Standard_D15i_v2|1|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Dv3-serien

| Size | Faktor|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Mer information finns i [generell användnings storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>ESv3-serien

| Size | Faktor|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>ESv3-serien, isolerad-serien

| Size | Faktor|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Ev3-serien

| Size | Faktor|
|---|---|
|Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Ev3-serien, isolerad-serien

| Size | Faktor|
|---|---|
|Standard_E64i_v3|1|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-serien

| Size | Faktor|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>FS-serien

| Size | Faktor|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Mer information finns i [tidigare generationer av virtuella dator storlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Fsv2-serien

| Size | Faktor|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Mer information finns i [Beräkna optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>H-serien

| Size | Faktor|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Mer information finns i [storlekar för beräkning av virtuella datorer med höga prestanda](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Hög minne i H-serien

| Size | Faktor|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Mer information finns i [storlekar för beräkning av virtuella datorer med höga prestanda](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>HCS UPPDATERINGSKLIENTEN-serien

| Size | Faktor|
|---|---|
|Standard_HC44rs|1|

Mer information finns i [storlekar för beräkning av virtuella datorer med höga prestanda](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>HBS-serien

| Size | Faktor|
|---|---|
|Standard_HB60rs|1|

Mer information finns i [storlekar för beräkning av virtuella datorer med höga prestanda](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>HBS-serien

| Size | Faktor|
|---|---|
|Standard_HB60rs|1|

Mer information finns i [storlekar för beräkning av virtuella datorer med höga prestanda](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>H-seriens låg latens – serie

| Size | Faktor|
|---|---|
|Standard_H16r|1|

Mer information finns i [Storage-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>H-seriens höga minnes latens med låg latens

| Size | Faktor|
|---|---|
|Standard_H16mr|1|

Mer information finns i [Storage-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>H-serien

| Size | Faktor|
|---|---|
|Standard_H8|1|
|Standard_H16|2|

Mer information finns i [Storage-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>LSv2 Series-serien

| Size | Faktor|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

Mer information finns i [Storage-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>M-serien

| Size | Faktor|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Bråk i M-serien

| Size | Faktor|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Stort stort minne i M-serien

| Size | Faktor|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Stor stor fraktion i M-serien

| Size | Faktor|
|---|---|
|Standard_M32ls|1|
|Standard_M64ls|2|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Hög minne i M-serien

| Size | Faktor|
|---|---|
|Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>MS-serien fraktioner, liten

| Size | Faktor|
|---|---|
|Standard_M32ls|1|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>MSv2-serien, hög minnes serie

| Size | Faktor|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>MSv2-serien

| Size | Faktor|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

Mer information finns i minnesoptimerade [storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC-serien

| Size | Faktor|
|---|---|
|Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2-serien

| Size | Faktor|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3-serien

| Size | Faktor|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND-serien

| Size | Faktor|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV-serien

| Size | Faktor|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

## <a name="nvsv3-series"></a>NVSv3-serien

| Size | Faktor|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>Slut latens för NDS-serien – serien

| Size | Faktor|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>NCSv3 serie med låg latens – serie

| Size | Faktor|
|---|---|
|Standard_NC24rs_v3|1|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>NCSv2 serie med låg latens – serie

| Size | Faktor|
|---|---|
|Standard_NC24rs_v2|1|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>NC-seriens låg latens-serie

| Size | Faktor|
|---|---|
|Standard_NC24r|1|

Mer information finns i [GPU-optimerade storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).





