---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 05820cc5f7b7d61d83f73ea5b62b05f8712e0997
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771212"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>VM-storlek flexibilitet med reserverade VM-instanser

Med en reserverad VM-instans som har optimerats för instans storlek flexibilitet, kan du köpa reservationen gäller storlekar för virtuella datorer (VM) i gruppen för serien av samma storlek. Till exempel om du köper en reservation för en VM-storlek som anges i tabellen DSv2-serien som Standard_DS5_v2, kan rabatten gäller för de fyra storlekarna som listas i samma tabellen:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Men den reservationsrabatten avser inte storlekar för virtuella datorer som listas i olika tabeller som vad som finns i tabellen DSv2-serien extra minne: Standard_DS11_v2, Standard_DS12_v2 och så vidare.

Hur många virtuella datorer rabatten gäller för beror på VM-storlek som du väljer när du köper en reservation i storleksgruppen för serien. Det beror också på storleken på de virtuella datorerna som du har som körs. Förhållandet mellan kolumnen som anges i tabellerna nedan jämförs relativa storleken för varje VM-storlek i gruppen. Använd förhållande värdet att beräkna hur rabatten gäller för de virtuella datorerna du har som körs.

## <a name="examples"></a>Exempel

I följande exempel används det för storlekar och förhållanden i tabellen DSv2-serien.

 Du köper en reserverad VM-instans med storlek Standard_DS4_v2 där förhållande eller relativa fotavtryck jämfört med andra storlekar i serien är 8.

- Scenario 1: Kör åtta stora Standard_DS1_v2 virtuella datorer med ett förhållande på 1. Din reservationsrabatten gäller för alla åtta för dessa virtuella datorer.
- Scenario 2: Kör två stora Standard_DS2_v2 virtuella datorer med ett förhållande på 2 vardera. Också köra storleksanpassas en Standard_DS3_v2 virtuell dator och ett förhållande på 4. Totala storleken är 2 + 2 + 4 = 8. Så att din reservationsrabatten gäller för alla tre av dessa virtuella datorer.
- Scenario 3: Kör en Standard_DS5_v2 med ett förhållande på 16. Din reservationsrabatten avser halva den Virtuella datorns beräkningskostnad.

Nedanstående avsnitt visas vilka storlekar finns i samma serie storleksgruppen när du köper en reserverad VM-instans optimerad exempelvis storlek flexibilitet.

## <a name="b-series"></a>B-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Mer information finns i [B-serien med burst virtuella datorstorlekar](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>B-serien extra minne

| Storlek | Förhållandet mellan|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Mer information finns i [B-serien med burst virtuella datorstorlekar](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>D-serien extra minne

| Storlek | Förhållandet mellan|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>DS-serien

| Storlek | Förhållandet mellan|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>DS-serien extra minne

| Storlek | Förhållandet mellan|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-serien

| Storlek | Förhållandet mellan|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>DSv2-serien extra minne

| Storlek | Förhållandet mellan|
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

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>DSv3-serien

| Storlek | Förhållandet mellan|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Mer information finns i [storlekar för virtuella datorer för generell användning](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Dv2-serien

| Storlek | Förhållandet mellan|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Dv2-serien extra minne

| Storlek | Förhållandet mellan|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv3-series"></a>Dv3-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Mer information finns i [storlekar för virtuella datorer för generell användning](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>ESv3-serien

| Storlek | Förhållandet mellan|
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
|Standard_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Ev3-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>FS-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Mer information finns i [föregående generationer av virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Fsv2-serien

| Storlek | Förhållandet mellan|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Mer information finns i [beräkningsoptimerade virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>H-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Mer information finns i [högpresterande compute VM-storlekar](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>H-serien extra minne

| Storlek | Förhållandet mellan|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Mer information finns i [högpresterande compute VM-storlekar](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Ls-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

Mer information finns i [lagringsoptimerade virtuella datorstorlekar](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>M-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>M-serien bråkdelar

| Storlek | Förhållandet mellan|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>M-serien bråkdelar extra minne

| Storlek | Förhållandet mellan|
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

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>M-serien bråkdelar stora

| Storlek | Förhållandet mellan|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>M-serien extra minne

| Storlek | Förhållandet mellan|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Mer information finns i [storlekar för virtuella datorer för den minnesoptimerade](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Mer information finns i [GPU-optimerad storlekar för virtuella datorer](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Mer information finns i [GPU-optimerad storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Mer information finns i [GPU-optimerad storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Mer information finns i [GPU-optimerad storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV-serien

| Storlek | Förhållandet mellan|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

Mer information finns i [GPU-optimerad storlekar för virtuella datorer](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


