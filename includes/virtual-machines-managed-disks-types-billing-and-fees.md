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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443402"
---
**Utgående dataöverföringar**: [Utgående dataöverföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures datacenter) debiteras för bandbreddsanvändning.

**Transaktioner**: Du faktureras för antalet transaktioner som du kan utföra på en hanterad disk som standard. Azure debiterar $0.0036 per 100 000 transaktioner för standardhårddiskar. Transaktioner omfattar både läs- och skrivåtgärder till lagringsutrymmet.

Standard SSD-enheter använder storlek på i/o på 256 KB. Om de data som överförs är mindre än 256 KB, är det en i/o-enhet. Större i/o-storlekar räknas som flera I/o för storlek på 256 KB. Till exempel räknas en 1 100 KB-i/o som fem i/o-enheter.

Det kostar ingenting för transaktioner för en hanterad disk i premium.

Detaljerad information om priser för Managed Disks finns i [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM reservation avgift

Virtuella Azure-datorer möjlighet att ange om de är kompatibla med ultra SSD. Ett ultra disk kompatibel virtuell dator allokerar dedikerad bandbreddskapacitet mellan beräkningsinstans för virtuell dator och blockera lagringsskalningsenhet att optimera prestanda och minska svarstiden. Lägger till den här funktionen på VM-resultatet reservation kostar pengar som endast gäller om du har aktiverat ultra disk-funktionen på den virtuella datorn utan att koppla en ultra disk till den. När ett ultra disken är ansluten till ultra disk kompatibla virtuella datorer, skulle det inte att använda den här avgiften. Denna kostnad är per virtuell processor har etablerats på den virtuella datorn.

Referera till den [Azure Disks sidan med priser](https://azure.microsoft.com/pricing/details/managed-disks/) ultra disk i diskar prisinformation.