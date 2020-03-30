---
title: Översikt över tillgänglighetsgrupper
description: Den här artikeln introducerar SQL Server-tillgänglighetsgrupper på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037487"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introduktion till SQL Server-tillgänglighetsgrupper på virtuella Azure-datorer

Den här artikeln introducerar SQL Server-tillgänglighetsgrupper på virtuella Azure-datorer. 

Alltid på tillgänglighet grupper på Azure virtuella datorer liknar Alltid på tillgänglighet grupper på plats. Mer information finns i [Alltid på tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagrammet illustrerar delarna i en komplett SQL Server-tillgänglighetsgrupp i Virtuella Azure-datorer.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Den viktigaste skillnaden för en tillgänglighetsgrupp i Virtuella Azure-datorer är att virtuella Azure-datorer kräver en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md). Belastningsutjämnaren innehåller IP-adresserna för tillgänglighetsgruppens lyssnare. Om du har mer än en tillgänglighetsgrupp kräver varje grupp en lyssnare. En belastningsutjämnare kan stödja flera lyssnare.

Dessutom rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät på ett azure IaaS VM-gästkon redundanskluster. Azures nätverk har fysisk redundans, vilket innebär att det inte behövs fler nätverkskort och undernät för gästkluster på virtuella Azure IaaS-datorer. Även om klustrets verifieringsrapport utfärdar en varning om att noderna endast kan nås i ett enda nätverk, kan varningen ignoreras för redundanskluster på virtuella Azure IaaS-gästdatorer. 

Om du vill öka redundansen och hög tillgänglighet bör virtuella SQL Server-datorer antingen vara i samma [tillgänglighetsuppsättning](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)eller olika [tillgänglighetszoner](/azure/availability-zones/az-overview). 

|  | Windows Server-version | SQL Server-version | SQL Server Edition | WSFC kvorum config | DR med flera regioner | Stöd för flera undernät | Stöd för en befintlig AD | DR med flera zoner samma region | Dist-AG-stöd utan AD-domän | Dist-AG-stöd utan kluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Molnvittne | Inga | Ja | Ja | Ja | Inga | Inga |
| [Snabbstartsmallar](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Molnvittne | Inga | Ja | Ja | Ja | Inga | Inga |
| [Portal-mall](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Filresurs | Inga | Inga | Inga | Inga | Inga | Inga |
| [Manuell](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Alla | Alla | Alla | Alla | Ja | Ja | Ja | Ja | Ja | Ja |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

När du är redo att skapa en SQL Server-tillgänglighetsgrupp på Virtuella Azure-datorer läser du dessa självstudier.

## <a name="manually-with-azure-cli"></a>Manuellt med Azure CLI
Att använda Azure CLI för att konfigurera och distribuera en tillgänglighetsgrupp är det rekommenderade alternativet, eftersom det är det bästa när det gäller enkelhet och snabb distribution. Med Azure CLI kan skapandet av Windows Failover Cluster, ansluta till virtuella SQL Server-datorer till klustret samt skapandet av lyssnaren och intern belastningsutjämnare uppnås på mindre än 30 minuter. Det här alternativet kräver fortfarande en manuell skapande av tillgänglighetsgruppen, men automatiserar alla andra nödvändiga konfigurationssteg. 

Mer information finns i [Använda Azure SQL VM CLI för att konfigurera alltid på tillgänglighetsgrupp för SQL Server på en Azure VM](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatiskt med Azure-snabbstartsmallar
Azure Quickstart-mallarna använder SQL VM-resursprovidern för att distribuera Windows Redundansklustret, ansluta virtuella SQL Server-datorer till den, skapa lyssnaren och konfigurera intern belastningsutjämning. Det här alternativet kräver fortfarande ett manuellt skapande av tillgänglighetsgruppen och Intern belastningsutjämnare (ILB), men automatiserar och förenklar alla andra nödvändiga konfigurationssteg (inklusive konfigurationen av ILB). 

Mer information finns i [Använda Azure Quickstart-mall för att konfigurera alltid på tillgänglighetsgrupp för SQL Server på en Virtuell Azure.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="automatically-with-an-azure-portal-template"></a>Automatiskt med en Azure-portalmall

[Konfigurera alltid på tillgänglighetsgrupp i Azure VM automatiskt - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuellt i Azure-portalen

Du kan också skapa de virtuella datorerna själv utan mallen. Slutför först förutsättningarna och skapa sedan tillgänglighetsgruppen. Se följande avsnitt: 

- [Konfigurera förutsättningar för SQL Server Always On-tillgänglighetsgrupper på virtuella Azure-datorer](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Skapa alltid på tillgänglighetsgrupp för att förbättra tillgänglighet och haveriberedskap](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Nästa steg

[Konfigurera en SQL-server som alltid är tillgänglig på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
