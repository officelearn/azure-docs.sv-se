---
title: SQL-servertillgänglighet grupper - virtuella Azure-datorer – översikt | Microsoft Docs
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
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396439"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introduktion till SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines #

Den här artikeln introducerar Tillgänglighetsgrupper för SQL Server på Azure Virtual Machines. 

Always On-Tillgänglighetsgrupper på Azure Virtual Machines liknar Always On-Tillgänglighetsgrupper lokalt. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Diagrammet visar de olika delarna av en fullständig SQL-Server tillgänglighetsgrupp i Azure Virtual Machines.

![Tillgänglighetsgruppen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Den viktigaste skillnaden för en tillgänglighetsgrupp i Azure Virtual Machines är att de virtuella Azure-datorerna, kräver en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md). Belastningsutjämnaren innehåller IP-adresser för tillgänglighetsgruppens lyssnare. Om du har mer än en tillgänglighetsgrupp kräver en lyssnare i varje grupp. En belastningsutjämnare kan stödja flera lyssnare.

När du är redo att skapa en SQL Server tillgänglighet aroup på Azure Virtual Machines, referera till dessa självstudier.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automatiskt skapa en tillgänglighetsgrupp från en mall

[Konfigurera Always On-tillgänglighetsgrupp i Azure VM automatiskt - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Skapa en tillgänglighetsgrupp manuellt i Azure-portalen

Du kan också skapa de virtuella datorerna själv utan mallen. Först måste uppfylla förutsättningar och sedan skapa tillgänglighetsgruppen. Finns i följande avsnitt: 

- [Konfigurera krav för SQL Server Always On-Tillgänglighetsgrupper på Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Skapa alltid på Tillgänglighetsgruppen att förbättra tillgänglighet och katastrofåterställning](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Nästa steg

[Konfigurera en SQLServer alltid på Tillgänglighetsgruppen på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md).
