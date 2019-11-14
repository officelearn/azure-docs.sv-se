---
title: Översikt över tillgänglighets grupper
description: Den här artikeln beskriver SQL Server tillgänglighets grupper på virtuella Azure-datorer.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037487"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introduktion till SQL Server tillgänglighets grupper på Azure Virtual Machines

I den här artikeln beskrivs SQL Server tillgänglighets grupper på Azure Virtual Machines. 

Always on-tillgänglighetsgrupper på Azure Virtual Machines liknar Always on-tillgänglighetsgrupper lokalt. Mer information finns i [Always on Availability groups (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagrammet illustrerar delarna i en fullständig SQL Server tillgänglighets grupp i Azure Virtual Machines.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Den viktigaste skillnaden för en tillgänglighets grupp i Azure Virtual Machines är att de virtuella Azure-datorerna kräver en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md). Belastningsutjämnaren innehåller IP-adresserna för tillgänglighets gruppens lyssnare. Om du har fler än en tillgänglighets grupp måste varje grupp ha en lyssnare. En belastningsutjämnare har stöd för flera lyssnare.

Dessutom rekommenderar vi att du använder ett enda nätverkskort per server (klusternod) och ett enda undernät på en Azure-IaaS för virtuella datorer i gäst klustret. Azures nätverk har fysisk redundans, vilket innebär att det inte behövs fler nätverkskort och undernät för gästkluster på virtuella Azure IaaS-datorer. Även om klustrets verifieringsrapport utfärdar en varning om att noderna endast kan nås i ett enda nätverk, kan varningen ignoreras för redundanskluster på virtuella Azure IaaS-gästdatorer. 

För att öka redundansen och hög tillgänglighet ska de virtuella SQL Server datorerna antingen finnas i samma [tillgänglighets uppsättning](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)eller olika [tillgänglighets zoner](/azure/availability-zones/az-overview). 

|  | Windows Server-version | SQL Server version | SQL Servers utgåva | Konfiguration av WSFC-kvorum | DR med flera regioner | Stöd för flera undernät | Stöd för en befintlig AD | DR med samma region med flera zoner | Dist-AG-stöd utan AD-domän | Förd-AG-stöd utan kluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | ENT | Molnvittne | Nej | Ja | Ja | Ja | Nej | Nej |
| [Snabb starts mallar](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | ENT | Molnvittne | Nej | Ja | Ja | Ja | Nej | Nej |
| [Portal mall](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | ENT | Filresurs | Nej | Nej | Nej | Nej | Nej | Nej |
| [Bok](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Alla | Alla | Alla | Alla | Ja | Ja | Ja | Ja | Ja | Ja |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

När du är redo att bygga en SQL Server tillgänglighets grupp på Azure Virtual Machines, se de här självstudierna.

## <a name="manually-with-azure-cli"></a>Manuellt med Azure CLI
Att använda Azure CLI för att konfigurera och distribuera en tillgänglighets grupp är det rekommenderade alternativet, eftersom det är det bästa vad gäller enkelhetens och snabbare distribution. Med Azure CLI kan du skapa ett Windows-redundanskluster, ansluta SQL Server virtuella datorer till klustret, samt att skapa lyssnare och interna Load Balancer kan uppnås under 30 minuter. Det här alternativet kräver fortfarande en manuell skapande av tillgänglighets gruppen, men automatiserar alla andra nödvändiga konfigurations steg. 

Mer information finns i [använda Azure SQL VM CLI för att konfigurera Always on-tillgänglighetsgrupper för SQL Server på en virtuell Azure-dator](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatiskt med Azure snabb starts mallar
Azure snabb starts-mallar använder SQL VM Resource Provider för att distribuera Windows-redundansklustret, ansluta SQL Server virtuella datorer till den, skapa lyssnaren och konfigurera interna Load Balancer. Det här alternativet kräver fortfarande en manuell skapande av tillgänglighets gruppen och interna Load Balancer (ILB) men automatiserar och fören klar alla andra nödvändiga konfigurations steg (inklusive konfiguration av ILB). 

Mer information finns i [använda Azure snabb starts mal len för att konfigurera Always on-tillgänglighetsgrupper för SQL Server på en virtuell Azure-dator](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatiskt med en Azure Portal-mall

[Konfigurera Always on tillgänglighets grupp i Azure VM automatiskt – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuellt i Azure Portal

Du kan också skapa de virtuella datorerna själv utan mallen. Slutför först kraven och skapa sedan tillgänglighets gruppen. Se följande avsnitt: 

- [Konfigurera förutsättningar för SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Skapa Always on-tillgänglighetsgrupper för att förbättra tillgängligheten och haveri beredskap](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Nästa steg

[Konfigurera en SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
