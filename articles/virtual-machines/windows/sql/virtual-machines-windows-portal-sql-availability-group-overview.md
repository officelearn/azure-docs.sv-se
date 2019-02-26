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
ms.openlocfilehash: 728d73ee954a6ddebf80c6a9c466784c9c79bd53
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819164"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introduktion till SQL Server Always On-Tillgänglighetsgrupper på virtuella Azure-datorer #

Den här artikeln ger en introduktion av SQL Server-Tillgänglighetsgrupper på Azure Virtual Machines. 

Always On-Tillgänglighetsgrupper på Azure Virtual Machines liknar ständigt aktiverade Tillgänglighetsgrupper lokalt. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagrammet visar delarna av en fullständig SQL Server-tillgänglighetsgrupp i Azure Virtual Machines.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Den viktigaste skillnaden för en tillgänglighetsgrupp i Azure Virtual Machines är att Azure-datorer, kräver en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md). Belastningsutjämnaren innehåller IP-adresser för tillgänglighetsgruppens lyssnare. Om du har mer än en tillgänglighetsgrupp måste varje grupp en lyssnare. En belastningsutjämnare har stöd för flera lyssnare.

Dessutom på en virtuell Azure IaaS-dator gästredundanskluster rekommenderar vi ett enda nätverkskort per server (klusternoden) och ett enda undernät. Azure-nätverk har fysiska redundans, vilket gör ytterligare nätverkskort och undernät onödiga på ett gästkluster för virtuella Azure IaaS-datorer. Även om verifieringsrapporten utfärdar en varning att noderna är endast kan nås i ett enda nätverk, kan den här varningen ignoreras på Azure IaaS VM-gästredundanskluster. 

|  | Windows Server Version | SQL Server Version | SQL Server-version | WSFC kvorum Config | DR med flera regioner | Stöd för flera undernät | Stöd för en befintlig AD | DR med flera zoner samma region | Dist AG-stöd med ingen AD-domän | Dist AG-stöd med inget kluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Molnvittne | Nej | Ja | Ja | Ja | Nej | Nej |
| [Snabbstartsmallar](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Molnvittne | Nej | Ja | Ja | Ja | Nej | Nej |
| [Mall](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Filresurs | Nej | Nej | Nej | Nej | Nej | Nej |
| [Manuell](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Alla | Alla | Alla | Alla | Ja | Ja | Ja | Ja | Ja | Ja |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

När du är redo att skapa en tillgänglighetsgrupp för SQL Server på Azure Virtual Machines, referera till de här självstudierna.

## <a name="manually-with-azure-cli"></a>Manuellt med Azure CLI
Använder Azure CLI för att konfigurera och distribuera en tillgänglighetsgrupp är det rekommenderade alternativet eftersom det är bäst när det gäller enkelhet och snabb distribution. Med Azure CLI, skapandet av Windows-redundanskluster, ansluter till SQL Server-datorer i klustret, samt att skapa lyssnaren och den interna belastningsutjämnaren kan alla ske inom 30 minuter. Det här alternativet kan du fortfarande kräver en manuell har skapats för tillgänglighetsgruppen, men automatiserar alla andra nödvändiga konfigurationssteg. 

Mer information finns i [Använd Azure SQL VM CLI för att konfigurera Always On-tillgänglighetsgrupp för SQL Server på en Azure VM](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatiskt med Azure-Snabbstartmallar
Azure-Snabbstartsmallar använda SQL VM-resursprovidern för att distribuera Windows-redundanskluster, ansluta till SQL Server-datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren. Det här alternativet kräver en manuell skapa tillgänglighetsgruppen och interna belastningsutjämnaren (ILB) men automatiserar fortfarande och förenklar alla andra nödvändiga konfigurationssteg (inklusive konfigurationen av den interna Belastningsutjämnaren). 

Mer information finns i [användning Azure-Snabbstartsmall för att konfigurera Always On-tillgänglighetsgrupp för SQL Server på en Azure VM](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatiskt med en mall för Azure

[Konfigurera Always On-tillgänglighetsgrupp i Azure VM automatiskt – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuellt i Azure-portalen

Du kan också skapa virtuella datorer själv utan mallen. Först slutföra förutsättningarna och sedan skapa tillgänglighetsgruppen. Finns i följande avsnitt: 

- [Konfigurera krav för SQL Server Always On-Tillgänglighetsgrupper på Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Skapa Always On Availability Group att förbättra tillgänglighet och katastrofåterställning](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Nästa steg

[Konfigurera en SQLServer Always On-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
