---
title: DV4 och Dsv4-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer med DV4 och Dsv4-serien.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 32520a458a797ee9945603dd86c9efe359e4d1f6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291174"
---
# <a name="dv4-and-dsv4-series"></a>DV4- och Dsv4-serien

DV4 och Dsv4-serien körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Den har en hållbar all Core Turbo klock hastighet på 3,4 GHz. 

> [!NOTE]
> För vanliga frågor, se [Azure VM-storlekar utan en lokal temporär disk](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>DV4-serien

DV4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Storlekarna i DV4-serien erbjuder en kombination av alternativ för vCPU, minne och Fjärrlagring för de flesta produktions arbets belastningar. Virtuella datorer i DV4-serien har [Intel &reg; Hyper-Threading-teknik](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer. Använd Dsv4-storlekarna om du vill använda Premium Storage-diskar. Pris-och debiterings mätare för Dsv4-storlekar är samma som för DV4-serien.


> [!IMPORTANT]
> Dessa nya storlekar finns för närvarande endast i offentlig för hands version. Du kan registrera dig för dessa DV4-och Dsv4-serier [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 


ACU: 195-210

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Endast Fjärrlagring | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Endast Fjärrlagring | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Endast Fjärrlagring | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Endast Fjärrlagring | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Endast Fjärrlagring | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Endast Fjärrlagring | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Endast Fjärrlagring | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4-serien

Dsv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Storlekarna i DV4-serien erbjuder en kombination av alternativ för vCPU, minne och Fjärrlagring för de flesta produktions arbets belastningar. Virtuella datorer i Dsv4-serien har [Intel &reg; Hyper-Threading-teknik](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer.

> [!IMPORTANT]
> Dessa nya storlekar finns för närvarande endast i offentlig för hands version. Du kan registrera dig för dessa DV4-och Dsv4-serier [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195-210

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat data flöde: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Endast Fjärrlagring | 4 | 19000/120 (50) | 3000/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Endast Fjärrlagring | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Endast Fjärrlagring | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Endast Fjärrlagring | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Endast Fjärrlagring | 32 | 308000/1936 (800) | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Endast Fjärrlagring | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Endast Fjärrlagring | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |
