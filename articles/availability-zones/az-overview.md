---
title: "Översikt över tillgänglighet zoner | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över tillgänglighet zoner i Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 3c8be3b1bc56697d42f2f280af756ed61533d1ef
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Översikt över tillgänglighet zoner i Azure (förhandsversion)

Tillgänglighet zoner hjälpa för att skydda dig från fel datacenter-nivå. De finns i en Azure-region och var och en har sin egen oberoende power käll-, nätverks- och kylning. För att säkerställa återhämtning, finns det minst tre separata zoner i alla aktiverade regioner. Fysiska och logiska avgränsning av tillgänglighet zoner inom en region skyddar program och data från fel på zonnivå. 

![Konceptuell visning av en zon gå i en region](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regioner som har stöd för tillgänglighet zoner

- Östra USA 2
- Västra Europa
- Frankrike, centrala

## <a name="services-that-support-availability-zones"></a>Tjänster som stöder tillgänglighet zoner

Azure-tjänster som stöder tillgänglighet zoner är:

- Virtuella Linux-datorer
- Virtuella Windows-datorer
- Zonal virtuella datorer
- Managed Disks
- Belastningsutjämnare
- Offentlig IP-adress

## <a name="supported-virtual-machine-size-families"></a>Virtuella datorer storlek familjer

- Av2
- Dv2
- DSv2


## <a name="get-started-with-the-availability-zones-preview"></a>Komma igång med tillgänglighet zoner preview

Tillgänglighet zoner förhandsgranskningen är tillgängligt i östra USA 2, västra Europa och Frankrike centrala områden för specifika Azure-tjänster. 

1. [Logga för zonerna tillgänglighet Förhandsgranska](http://aka.ms/azenroll). 
2. Logga in på Azure-prenumerationen.
3. Välj en region som stöder tillgänglighet zoner.
4. Använd någon av följande länkar för att börja använda tillgänglighet zoner med din tjänst. 
    - [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Skapa en zonal virtuella datorns skaluppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)
    - [Lägga till en hanteras med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Belastningsutjämnare](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](http://aka.ms/azqs)
