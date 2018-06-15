---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 0b6846a68806354a58516fcbc87913815af87343
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29958749"
---
Vissa arbetsbelastningar som SQL Server eller Oracle kräver RAM-minne, lagring, och i/o-bandbredd, men inte antalet hög kärnor. Många databasarbetsbelastningar är inte processorintensiva. Azure erbjuder vissa VM-storlekar där du kan begränsa antalet VM vCPU att minska kostnaden för programvarulicensiering samtidigt samma minne, lagring och i/o-bandbredd.

Antalet vCPU kan begränsas till hälften eller en kvartal ursprungliga VM-storlek. Dessa nya VM-storlekar ha ett suffix som anger antalet aktiva vCPUs så att de blir enklare att identifiera.

Till exempel den aktuella VM-storleken Standard_GS5 medföljer 32 vCPUs, 448 GB RAM-minne, 64 diskar (upp till 256 TB) och 80 000 IOPs eller 2 GBIT/s av i/o-bandbredd. Den nya VM-storlekar Standard_GS5 16 och Standard_GS5 8 medföljer 16 och 8 active vCPUs, samtidigt som resten av specifikationer för Standard_GS5 för minne, lagring och i/o-bandbredd.

Licensiering avgifter debiteras för SQL Server eller Oracle är begränsade till antalet vCPU och andra produkter bör debiteras baserat på antalet vCPU. Detta resulterar i en ökning förhållandet mellan VM-specifikationerna 50 till 75% till aktiv (fakturerbar) vCPUs. Dessa nya VM-storlekar som endast är tillgängliga i Azure, så att arbetsbelastningar till push högre CPU-användning på en bråkdel av licensiering (per kärna) kostnad. Beräkning kostnad, vilket innefattar OS-licensiering, förblir just nu är det samma som den ursprungliga storleken. Mer information finns i [Azure VM-storlekar för mer kostnadseffektiv databasarbetsbelastningar](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Namn                | Virtuell processor | Specifikationer           |
|---------------------|------|-----------------|
| Standard_M64-32ms   | 32   | Samma som M64ms   |
| Standard_M64-16ms   | 16   | Samma som M64ms   |
| Standard_M128-64ms  | 64   | Samma som M128ms  |
| Standard_M128-32ms  | 32   | Samma som M128ms  |
| Standard_E4-2s_v3   | 2    | Same as E4s_v3  |
| Standard_E8-4s_v3   | 4    | Same as E8s_v3  |
| Standard_E8-2s_v3   | 2    | Same as E8s_v3  |
| Standard_E16-8s_v3  | 8    | Samma som E16s_v3 |
| Standard_E16-4s_v3  | 4    | Samma som E16s_v3 |
| Standard_E32-16_v3  | 16   | Same as E32s_v3 |
| Standard_E32-8s_v3  | 8    | Same as E32s_v3 |
| Standard_E64-32s_v3 | 32   | Same as E64s_v3 |
| Standard_E64-16s_v3 | 16   | Same as E64s_v3 |
| Standard_GS4-8      | 8    | Samma som GS4     |
| Standard_GS4-4      | 4    | Samma som GS4     |
| Standard_GS5-16     | 16   | Samma som GS5     |
| Standard_GS5-8      | 8    | Samma som GS5     |
| Standard_DS11-1_v2  | 1    | Same as DS11_v2 |
| Standard_DS12-2_v2  | 2    | Same as DS12_v2 |
| Standard_DS12-1_v2  | 1    | Same as DS12_v2 |
| Standard_DS13-4_v2  | 4    | Same as DS13_v2 |
| Standard_DS13-2_v2  | 2    | Same as DS13_v2 |
| Standard_DS14-8_v2  | 8    | Same as DS14_v2 |
| Standard_DS14-4_v2  | 4    | Same as DS14_v2 |
