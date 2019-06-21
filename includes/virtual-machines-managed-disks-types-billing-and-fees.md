---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187330"
---
**Utgående dataöverföringar**: [Utgående dataöverföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures datacenter) debiteras för bandbreddsanvändning.

**Transaktioner**: Du faktureras för antalet transaktioner som du kan utföra på en hanterad disk som standard. För standard SSD: er varje i/o-åtgärden har mindre än eller lika med 256 KiB dataflödets betraktas som en enda i/o-åtgärd. I/o-åtgärder större än 256 KiB dataflödets anses flera I/o med storleken 256 KiB. För standardhårddiskar anses varje i/o-åtgärd som en enda transaktion, oavsett i/o-storlek.

Detaljerad information om priser för Managed Disks, inklusive transaktionskostnader, finns i [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM reservation avgift

Virtuella Azure-datorer möjlighet att ange om de är kompatibla med ultra SSD. Ett ultra disk kompatibel virtuell dator allokerar dedikerad bandbreddskapacitet mellan beräkningsinstans för virtuell dator och blockera lagringsskalningsenhet att optimera prestanda och minska svarstiden. Lägger till den här funktionen på VM-resultatet reservation kostar pengar som endast gäller om du har aktiverat ultra disk-funktionen på den virtuella datorn utan att koppla en ultra disk till den. När ett ultra disken är ansluten till ultra disk kompatibla virtuella datorer, skulle det inte att använda den här avgiften. Denna kostnad är per virtuell processor har etablerats på den virtuella datorn.

Referera till den [Azure Disks sidan med priser](https://azure.microsoft.com/pricing/details/managed-disks/) ultra disk i diskar prisinformation.