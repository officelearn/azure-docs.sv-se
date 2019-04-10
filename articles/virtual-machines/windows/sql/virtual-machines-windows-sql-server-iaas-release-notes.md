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
ms.date: 2/13/2019
ms.openlocfilehash: 23e072369aa8ac6ca6ada5ec185df1a8d7e03c5b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282550"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server på Azure-dator viktig information

Azure kan du distribuera en virtuell dator med en bild av SQL Server som är inbyggda i. Den här artikeln sammanfattas de nya funktionerna och förbättringarna i de senaste versionerna av [SQL Server på Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). Artikeln innehåller också viktiga uppdateringar av innehållet som är inte direkt relaterade till versionen men som har publicerats i samma tidsram. Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates)

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Anpassad avbildning support** | Nu kan du installera den [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md#installation) för anpassade OS- och SQL-avbildningar, som erbjuder begränsad funktionalitet för [flexibla licensiering](virtual-machines-windows-sql-ahb.md). När registrera en anpassad avbildning med SQL-resursprovider anger licenstypen som ”AHUB” som annars misslyckas registreringen.  | 
| **Namngiven instans support** | Du kan nu använda den [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md#installation) med en namngiven instans om standardinstansen har avinstallerats korrekt. | 
| **Förbättring av portalen** | Azure-portalen tidigare för att distribuera en SQL Server VM har har gjort om för att förbättra användbarhet. Mer information finns i rapport [snabbstarten](quickstart-sql-vm-create-portal.md) och mer omfattande [How-to](virtual-machines-windows-portal-sql-server-provision.md) vägledning för distribution av en SQL Server VM. |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

Ingen


## <a name="february-2019"></a>Februari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Portalen förbättring** | Nu är det möjligt att ändra så att licensieringsmodellen för en SQL Server-VM från betala per användning till bring-your-own-license med hjälp av den [Azure-portalen](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**AG distribution förenkling av distribution med Azure SQL-dator med CLI** | Nu är det enklare än någonsin att distribuera en tillgänglighetsgrupp till en SQL Server-VM i Azure. [Azure SQL-dator med CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) kan du skapa WSFC, ILB och AG-lyssnare allt från kommandoraden och registrera tid! Mer information finns i [Använd Azure SQL VM CLI för att konfigurera Always On-tillgänglighetsgrupp för SQL Server på en Azure VM](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>December 2018

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny SQL-kluster grupp-resursprovider** | En ny resursprovider (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) som definierar metadata för Windows-redundanskluster. Ansluta till en SQL Server-VM till den *SqlVirtualMachineGroups* startar tjänsten Windows-redundanskluster och kopplar den virtuella datorn till klustret.  |
|**Automatisera konfigurationen av distribueringen av en tillgänglighet med Azure-Snabbstartsmallar** |Nu är det möjligt att skapa Windows-redundanskluster, ansluta till SQL Server-datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren med två Azure-Snabbstartsmallar. Mer information finns i [användning Azure-Snabbstartsmall för att konfigurera Always On-tillgänglighetsgrupp för SQL Server på en Azure VM](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Den automatiska SQL VM Resursproviderregistrering** | SQL Server-datorer distribueras efter det att den här månaden registreras automatiskt med den nya SQL Server-resursprovidern. SQL Server-datorer som distribuerats före den här månaden fortfarande behöver registreras manuellt. Mer information finns i [registrera befintliga SQL-VM med SQL VM-resursprovidern](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny SQL-VM-resursprovider** |  En ny resource provider för SQL Server-datorer (Microsoft.SqlVirtualMachine) som ger bättre hantering av SQL Server-dator. Mer information om hur du registrerar din virtuella dator finns i [registrera befintliga SQL-VM med ny resursprovider](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Växla licensieringsmodell** |Du kan växla mellan modellen betala per användning och bring-your-own-licens för din SQL-VM med Azure CLI eller Powershell. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Ytterligare resurser

**Virtuella Windows-datorer**:

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en virtuell dator med SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQLServer på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och katastrofåterställning för SQLServer på Azure virtuella datorer](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md)
* [Programmönster och utvecklingsstrategier för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQLServer på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
