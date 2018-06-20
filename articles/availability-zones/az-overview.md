---
title: Vad är Azure tillgänglighet zoner? | Microsoft Docs
description: Om du vill skapa hög tillgänglighet och flexibel program i Azure ger tillgänglighet zoner fysiskt separata platser som du kan använda för att köra dina resurser.
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 7cfd3fba459f409079cb0c621edab3197070285e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232425"
---
# <a name="what-are-availability-zones-in-azure"></a>Vad är tillgänglighet zoner i Azure?
Tillgänglighet zoner är en hög tillgänglighet erbjudande som skyddar program och data från fel datacenter. Tillgänglighet zoner är unika fysiska platser i en Azure-region. Varje zon består av en eller flera datacenter med oberoende ström, kylning och nätverk. För att säkerställa återhämtning, finns det minst tre separata zoner i alla aktiverade regioner. Fysisk avgränsning av tillgänglighet zoner inom en region skyddar program och data från fel datacenter. Zonredundant services replikeras dina program och data tillgänglighet zoner för att skydda från enkel punkter av fel. Azure erbjuder branschen bästa 99,99% VM drifttid SLA med tillgänglighet zoner. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

En zon för tillgänglighet i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Till exempel om du skapar tre eller flera virtuella datorer mellan tre zoner i en Azure-region, distribueras din virtuella dator effektivt över tre feldomäner och tre update domäner. Azure-plattformen känner igen den här distributionen update domäner och kontrollera att virtuella datorer i olika zoner inte uppdateras på samma gång.

Skapa hög tillgänglighet i din programarkitektur genom att samordna resurserna bearbetning, lagring, nätverk och data inom en zon och replikerar i andra zoner. Azure-tjänster som stöder tillgänglighet zoner är indelade i två kategorier:

- **Zonal services** – du kan fästa resurser till en viss zon (till exempel virtuella datorer, hanterade diskar, IP-adresser), eller
- **Zonredundant services** – plattform replikerar automatiskt över zoner (till exempel zonredundant lagring, SQL-databas).

Skapa din programarkitektur med Azure-region par kombinationen av tillgänglighet zoner för att få omfattande affärskontinuitet på Azure. Du kan synkront replikera dina program och data med hjälp av tillgänglighet zoner i en Azure-region för hög tillgänglighet och replikeras asynkront via Azure-regioner för disaster recovery-skydd.
 
![Konceptuell visning av en zon gå i en region](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regioner som har stöd för tillgänglighet zoner

- Centrala USA
- Frankrike, centrala
- Östra USA 2 (förhandsgranskning)
- Västra Europa (förhandsgranskning)
- Sydostasien (förhandsgranskning)


## <a name="services-that-support-availability-zones"></a>Tjänster som stöder tillgänglighet zoner
Azure-tjänster som stöder tillgänglighet zoner är:

- Virtuella Linux-datorer
- Virtuella Windows-datorer
- Virtual Machine Scale Sets
- Managed Disks
- Load Balancer
- Offentlig IP-adress
- Zonredundant lagring
- SQL Database


## <a name="pricing"></a>Prissättning
Det finns utan extra kostnad för virtuella datorer i en zon för tillgänglighet. Drifttid för 99,99% VM SLA erbjuds när två eller flera virtuella datorer distribueras mellan två eller flera tillgänglighet zoner i en Azure-region. Det blir ytterligare mellan tillgänglighet zonen VM-till-VM data transfer avgifter. Mer information finns i [bandbredd priser](https://azure.microsoft.com/pricing/details/bandwidth/) sidan.


## <a name="get-started-with-availability-zones"></a>Kom igång med tillgänglighet zoner
- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanteras med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en zon redundanta virtuella datorns skaluppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer i zoner som Standard belastningsutjämning med en zonredundant klientdel](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Belastningsutjämna virtuella datorer i en zon med en zonal klientdel Standard belastningsutjämning](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zonredundant lagring](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)


## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](http://aka.ms/azqs)
