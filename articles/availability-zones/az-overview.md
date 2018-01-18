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
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 2404575d7f4224315b2c3272cb21e8b5ee6515e3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Översikt över tillgänglighet zoner i Azure (förhandsversion)

Tillgänglighet zoner hjälpa för att skydda dig från fel datacenter-nivå. De finns i en Azure-region och var och en har sin egen oberoende power käll-, nätverks- och kylning. För att säkerställa återhämtning, finns det minst tre separata zoner i alla aktiverade regioner. Fysiska och logiska avgränsning av tillgänglighet zoner inom en region skyddar program och data från fel på zonnivå. 

![Konceptuell visning av en zon gå i en region](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regioner som har stöd för tillgänglighet zoner

- Östra USA 2
- Centrala USA
- Västra Europa
- Frankrike, centrala

## <a name="services-that-support-availability-zones"></a>Tjänster som stöder tillgänglighet zoner

Azure-tjänster som stöder tillgänglighet zoner är:

- Virtuella Linux-datorer
- Virtuella Windows-datorer
- Skalningsuppsättningar för Virtual Machines
- Managed Disks
- Belastningsutjämnare
- Offentlig IP-adress

## <a name="get-started-with-the-availability-zones-preview"></a>Komma igång med tillgänglighet zoner preview

Tillgänglighet zoner förhandsgranskningen är tillgängligt i östra USA 2, oss Central, västra Europa och Frankrike centrala områden för specifika Azure-tjänster. 

1. [Logga för zonerna tillgänglighet Förhandsgranska](http://aka.ms/azenroll). 
2. Logga in på Azure-prenumerationen.
3. Välj en region som stöder tillgänglighet zoner.
4. Använd någon av följande länkar för att börja använda tillgänglighet zoner med din tjänst. 
    - [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Skapa en skaluppsättning för virtuell dator](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [Lägga till en hanteras med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Belastningsutjämnare](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](http://aka.ms/azqs)
