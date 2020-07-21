---
title: ta med fil
description: ta med fil
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: mimckitt
ms.custom: include file
ms.openlocfilehash: ed1341a323d947441fa2cb3c239fcdf99c447a97
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494502"
---
Vissa databas arbets belastningar som SQL Server eller Oracle kräver hög minnes-, lagrings-och I/O-bandbredd, men inte ett högt antal kärnor. Många databas arbets belastningar är inte processor intensiva. Azure erbjuder vissa VM-storlekar där du kan begränsa antalet VM-vCPU för att minska kostnaderna för program varu licensiering, samtidigt som du behåller samma minne, lagring och I/O-bandbredd.

Antalet vCPU kan begränsas till en halv eller ett kvartal av den ursprungliga storleken på den virtuella datorn. Dessa nya VM-storlekar har ett suffix som anger antalet aktiva virtuella processorer som gör det enklare för dig att identifiera.

Till exempel innehåller den aktuella virtuella dator storleken Standard_GS5 32 virtuella processorer, 448 GB RAM, 64 diskar (upp till 256 TB) och 80 000 IOPs eller 2 GB/s i/O-bandbredd. De nya VM-storlekarna Standard_GS5-16 och Standard_GS5-8 levereras med 16 respektive 8 aktiva virtuella processorer, samtidigt som resten av specifikationerna för Standard_GS5 för minne, lagring och I/O-bandbredd bibehålls.

De licens avgifter som debiteras för SQL Server eller Oracle är begränsade till det nya antalet vCPU och andra produkter bör debiteras baserat på det nya vCPU antalet. Detta resulterar i en 50% till 75% ökning av förhållandet mellan VM-specifikationerna och aktiva (fakturerbara) virtuella processorer. Dessa nya VM-storlekar gör att kundernas arbets belastningar kan använda samma minne, lagrings utrymme och I/O-bandbredd samtidigt som program varu licensierings kostnaden optimeras. För närvarande är beräknings kostnaden, som innehåller OS-licensiering, densamma som den ursprungliga storleken. Mer information finns i [storlekar för virtuella Azure-datorer för mer kostnads effektiva databas arbets belastningar](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name                | Virtuell processor | Specifikationer           |
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
| Standard_E4-2s_v3   | 2    | Samma som E4s_v3  |
| Standard_E8-4s_v3   | 4    | Samma som E8s_v3  |
| Standard_E8-2s_v3   | 2    | Samma som E8s_v3  |
| Standard_E16-8s_v3  | 8    | Samma som E16s_v3 |
| Standard_E16-4s_v3  | 4    | Samma som E16s_v3 |
| Standard_E32-16s_v3 | 16   | Samma som E32s_v3 |
| Standard_E32-8s_v3  | 8    | Samma som E32s_v3 |
| Standard_E64-32s_v3 | 32   | Samma som E64s_v3 |
| Standard_E64-16s_v3 | 16   | Samma som E64s_v3 |
| Standard_E4-2s_v4   | 2    | Samma som E4s_v4  |
| Standard_E8-4s_v4   | 4    | Samma som E8s_v4  |
| Standard_E8-2s_v4   | 2    | Samma som E8s_v4  |
| Standard_E16-8s_v4  | 8    | Samma som E16s_v4 |
| Standard_E16-4s_v4  | 4    | Samma som E16s_v4 |
| Standard_E32-16s_v4 | 16   | Samma som E32s_v4 |
| Standard_E32-8s_v4  | 8    | Samma som E32s_v4 |
| Standard_E64-32s_v4 | 32   | Samma som E64s_v4 |
| Standard_E64-16s_v4 | 16   | Samma som E64s_v4 |
| Standard_E4-2ds_v4  | 2    | Samma som E4ds_v4 |
| Standard_E8-4ds_v4  | 4    | Samma som E8ds_v4 |
| Standard_E8-2ds_v4  | 2    | Samma som E8ds_v4 |
| Standard_E16-8ds_v4 | 8    | Samma som E16ds_v4|
| Standard_E16-4ds_v4 | 4    | Samma som E16ds_v4|
| Standard_E32-16ds_v4| 16   | Samma som E32ds_v4|
| Standard_E32-8ds_v4 | 8    | Samma som E32ds_v4|
| Standard_E64-32ds_v4| 32   | Samma som E64ds_v4|
| Standard_E64-16ds_v4| 16   | Samma som E64ds_v4|
| Standard_GS4-8      | 8    | Samma som GS4     |
| Standard_GS4-4      | 4    | Samma som GS4     |
| Standard_GS5-16     | 16   | Samma som GS5     |
| Standard_GS5-8      | 8    | Samma som GS5     |
| Standard_DS11-1_v2  | 1    | Samma som DS11_v2 |
| Standard_DS12-2_v2  | 2    | Samma som DS12_v2 |
| Standard_DS12-1_v2  | 1    | Samma som DS12_v2 |
| Standard_DS13-4_v2  | 4    | Samma som DS13_v2 |
| Standard_DS13-2_v2  | 2    | Samma som DS13_v2 |
| Standard_DS14-8_v2  | 8    | Samma som DS14_v2 |
| Standard_DS14-4_v2  | 4    | Samma som DS14_v2 |
| Standard_M416-208s_v2 | 208    | Samma som M416s_v2|
| Standard_M416-208ms_v2 | 208    | Samma som M416ms_v2 |
