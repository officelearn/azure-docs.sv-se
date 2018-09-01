---
title: Tillgänglighet för SQL Server grupperar - Azure-datorer – översikt | Microsoft Docs
description: Den här artikeln introducerar SQL Server-Tillgänglighetsgrupper på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b71ba12f6c533d67b04366f05b9334e1993823fa
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382036"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introduktion till SQL Server Always On-Tillgänglighetsgrupper på virtuella Azure-datorer #

Den här artikeln ger en introduktion av SQL Server-Tillgänglighetsgrupper på Azure Virtual Machines. 

Always On-Tillgänglighetsgrupper på Azure Virtual Machines liknar ständigt aktiverade Tillgänglighetsgrupper lokalt. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Diagrammet visar delarna av en fullständig SQL Server-tillgänglighetsgrupp i Azure Virtual Machines.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Den viktigaste skillnaden för en tillgänglighetsgrupp i Azure Virtual Machines är att Azure-datorer, kräver en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md). Belastningsutjämnaren innehåller IP-adresser för tillgänglighetsgruppens lyssnare. Om du har mer än en tillgänglighetsgrupp måste varje grupp en lyssnare. En belastningsutjämnare har stöd för flera lyssnare.

När du är redo att skapa en tillgänglighetsgrupp för SQL Server på Azure Virtual Machines, referera till de här självstudierna.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automatiskt skapa en tillgänglighetsgrupp från en mall

[Konfigurera Always On-tillgänglighetsgrupp i Azure VM automatiskt – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Manuellt skapa en tillgänglighetsgrupp i Azure portal

Du kan också skapa virtuella datorer själv utan mallen. Först slutföra förutsättningarna och sedan skapa tillgänglighetsgruppen. Finns i följande avsnitt: 

- [Konfigurera krav för SQL Server Always On-Tillgänglighetsgrupper på Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Skapa Always On Availability Group att förbättra tillgänglighet och katastrofåterställning](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Nästa steg

[Konfigurera en SQLServer Always On-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md).
