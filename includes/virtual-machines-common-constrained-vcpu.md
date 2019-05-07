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
ms.openlocfilehash: 360674514aff79908dcab8c8b93095ca2feb5d51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199511"
---
Vissa arbetsbelastningar som SQL Server- eller Oracle kräver extra minne, lagring, och i/o-bandbredd, men inte ett antal kärnor för hög. Många databasarbetsbelastningar är inte processorintensiva. Azure erbjuder vissa storlekar på Virtuella datorer där du kan begränsa vCPU-antal virtuella datorer för att minska kostnaden för programvarulicensiering samtidigt på samma minne, lagring och i/o-bandbredd.

Antal virtuella processorer kan begränsas till hälften eller en fjärdedel av den ursprungliga virtuella datorstorleken. De här nya VM-storlekarna ha ett suffix som anger antalet aktiva virtuella processorer så att de blir enklare för dig att identifiera.

Exempelvis den aktuella storleken för virtuell standard gs5 levereras med 32 virtuella processorer, 448 GB RAM-minne, 64 diskar (upp till 256 TB) och 80 000 IOPs eller 2 GB/s i/o-bandbredd. Den nya virtuella datorn storlekar standard gs5-16- och standard gs5-8 levereras med 16 och 8 active virtuella processorer, samtidigt som resten av specifikationer för standard gs5 för minne, lagring och i/o-bandbredd.

Licensiering avgifter debiteras för SQL Server- eller Oracle är begränsade till det nya antalet virtuella processorer och andra produkter som ska debiteras baserat på antalet virtuella processorer. Detta resulterar i en 50% till 75% ökning förhållandet mellan VM-specifikationer till aktiv (fakturerbara) virtuella processorer. Dessa nya storlekar på virtuella datorer som bara är tillgängliga i Azure, innebär att arbetsbelastningarna kan ha en större CPU-användning till en bråkdel av licenskostnaden (per kärna). Beräkningskostnaden, vilket innefattar OS licensiering, förblir det samma som den ursprungliga storleken för tillfället. Mer information finns i [Azure VM-storlekar för mer kostnadseffektiv databasarbetsbelastningar](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Namn                | Virtuell processor | Specifikationer           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Samma som M8ms    |
| Standard_M8-4ms     | 4    | Samma som M8ms    |
| Standard_M16-4ms    | 4    | Samma som M16ms   |
| Standard_M16-8ms    | 8    | Samma som M16ms   |
| Standard_M32-8ms    | 8    | Samma som M32ms   |
| Standard_M32-16ms   | 16   | Samma som M32ms   |
| Standard_M64-32ms   | 32   | Samma som M64ms   |
| Standard_M64-16ms   | 16   | Samma som M64ms   |
| Standard_M128-64ms  | 64   | Samma som M128ms  |
| Standard_M128-32ms  | 32   | Samma som M128ms  |
| Standard_E4-2s_v3   | 2    | Same as E4s_v3  |
| Standard_E8-4s_v3   | 4    | Same as E8s_v3  |
| Standard_E8-2s_v3   | 2    | Same as E8s_v3  |
| Standard_E16-8s_v3  | 8    | Same as E16s_v3 |
| Standard_E16-4s_v3  | 4    | Same as E16s_v3 |
| Standard_E32-16s_v3 | 16   | Same as E32s_v3 |
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
