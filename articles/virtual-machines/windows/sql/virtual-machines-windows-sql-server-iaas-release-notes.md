---
title: SQLServer på Azure VM viktig information | Microsoft Docs
description: Lär dig mer om nya funktioner och förbättringar av SQL Server på en Azure VM
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: e3f44181ca1a5ea64815aadf52aa7ea792a21416
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358583"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server på Azure-dator viktig information

Azure kan du distribuera en virtuell dator med en bild av SQL Server som är inbyggda i. Den här artikeln innehåller nya funktioner och förbättringar som du kan förvänta dig i den senaste versionen av SQL Server distribueras på virtuella Azure-datorer. 

## <a name="december-2018"></a>December 2018

| **Ändra** | Information |
| --- | --- |
| **Ny SQL-kluster grupp-resursprovider** | Det finns en ny resursprovider (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroup) som definierar metadata om Windows-redundanskluster. Ansluta till en SQL Server-VM till den *SqlVirtualMachineGroup* startar tjänsten Windows-redundanskluster och kopplar den virtuella datorn till klustret.  |
|**Automatisera konfigurationen av distribueringen av en tillgänglighet med Azure-Snabbstartsmallar** |Nu är det möjligt att skapa Windows-redundanskluster, ansluta till SQL Server-datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren med två Azure-Snabbstartsmallar. Mer information finns i [skapa WSFC, lyssnaren och och konfigurera ILB för en Always On-tillgänglighetsgrupp på en SQL Server-dator med Azure-Snabbstartsmall](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Den automatiska SQL VM Resursproviderregistrering** | SQL Server-datorer distribueras efter det att den här månaden registreras automatiskt med den nya SQL Server-resursprovidern. SQL Server-datorer som distribuerats före den här månaden måste registreras manuellt. Mer information finns i [registrera befintliga SQL-VM med ny resursprovider](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| **Ändra** | Information |
| --- | --- |
| **Ny SQL-VM-resursprovider** |  Det finns en ny resource provider för SQL Server-datorer (Microsoft.SqlVirtualMachine) som möjliggör bättre hantering av SQL Server-dator. Mer information om hur du registrerar din virtuella dator finns i [registrera befintliga SQL-VM med ny resursprovider](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider). |
|**Växla licensieringsmodell** |Du kan växla mellan modellen betala per användning och bring-your-own-licens för din SQL-VM med Azure CLI eller Powershell. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


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
* [Etablera en SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
