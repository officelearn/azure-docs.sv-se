---
title: SQLServer på Azure VM viktig information | Microsoft Docs
description: Lär dig mer om nya funktioner och förbättringar av SQL Server på en Azure VM
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/5/2019
ms.openlocfilehash: b59ac4798260381c11ab22adb7358ff63e5e1d77
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245430"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server på Azure-dator viktig information

Azure kan du distribuera en virtuell dator med en bild av SQL Server som är inbyggda i. Den här artikeln sammanfattas de nya funktionerna och förbättringarna i de senaste versionerna av [SQL Server på Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). Artikeln innehåller också viktiga uppdateringar av innehållet som är inte direkt relaterade till versionen men som har publicerats i samma tidsram. Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates)

## <a name="december-2018"></a>December 2018

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny SQL-kluster grupp-resursprovider** | En ny resursprovider (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) som definierar metadata för Windows-redundanskluster. Ansluta till en SQL Server-VM till den *SqlVirtualMachineGroups* startar tjänsten Windows-redundanskluster och kopplar den virtuella datorn till klustret.  |
|**Automatisera konfigurationen av distribueringen av en tillgänglighet med Azure-Snabbstartsmallar** |Nu är det möjligt att skapa Windows-redundanskluster, ansluta till SQL Server-datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren med två Azure-Snabbstartsmallar. Mer information finns i [skapa WSFC, lyssnaren och och konfigurera ILB för en Always On-tillgänglighetsgrupp på en SQL Server-dator med Azure-Snabbstartsmall](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Den automatiska SQL VM Resursproviderregistrering** | SQL Server-datorer distribueras efter det att den här månaden registreras automatiskt med den nya SQL Server-resursprovidern. SQL Server-datorer som distribuerats före den här månaden fortfarande behöver registreras manuellt. Mer information finns i [registrera befintliga SQL-VM med ny resursprovider](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|inga | |
| | |

## <a name="november-2018"></a>November 2018

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny SQL-VM-resursprovider** |  En ny resource provider för SQL Server-datorer (Microsoft.SqlVirtualMachine) som ger bättre hantering av SQL Server-dator. Mer information om hur du registrerar din virtuella dator finns i [registrera befintliga SQL-VM med ny resursprovider](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Växla licensieringsmodell** |Du kan växla mellan modellen betala per användning och bring-your-own-licens för din SQL-VM med Azure CLI eller Powershell. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|inga | |
| | |

## <a name="additional-resources"></a>Ytterligare resurser

**Virtuella Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en virtuell dator med SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQLServer på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och katastrofåterställning för SQLServer på Azure virtuella datorer](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Programmönster och utvecklingsstrategier för SQLServer på Azure virtuella datorer](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)