---
title: Viktig information om SQL Server på Azure VM | Microsoft Docs
description: Lär dig mer om de nya funktionerna och förbättringarna av SQL Server på en virtuell Azure-dator
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
ms.date: 08/01/2019
ms.openlocfilehash: e656cd4d901ad9f3180963047a85cd980e56df8b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774205"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Viktig information om SQL Server på Azure Virtual Machine

Med Azure kan du distribuera en virtuell dator med en avbildning av SQL Server inbyggd. Den här artikeln sammanfattar de nya funktionerna och förbättringarna i de senaste versionerna av [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). Artikeln innehåller också viktiga innehålls uppdateringar som inte är direkt relaterade till versionen men som publiceras i samma tidsram. Förbättringar av andra Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates)

## <a name="july-2019"></a>Juli 2019

### <a name="documentation-improvements"></a>Dokumentations förbättringar

| Dokumentation | Information |
| --- | --- |
| **Flytta SQL VM till en annan region** | Använd Azure Site Recovery för att [migrera SQL Server VM från en region till en annan](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>2019 juni

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Nya installations lägen för SQL-IaaS** | Nu är det möjligt att installera SQL IaaS-tillägget i [lättviktigt läge](virtual-machines-windows-sql-server-agent-extension.md) för att undvika att starta om SQL Server-tjänsten.  |
| **Ändring av SQL Servers utgåva** | Nu kan du ändra [egenskapen version](virtual-machines-windows-sql-change-edition.md) för SQL Server VM. |
| **SQL VM RP-ändringar** | Du kan [Registrera din SQL Server VM med SQL VM Resource Provider](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) – även [Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) -avbildningar – med de nya SQL IaaS-lägena. |
| **BYOL-avbildningar med AHUB** | BYOL-avbildningar som distribueras från Marketplace kan nu byta [licens typ till "PAYG"](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Maj 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny hantering av virtuella SQL-datorer i Azure Portal** | Det finns nu ett nytt sätt att hantera SQL Server VM i Azure Portal. Mer information finns i [hantera SQL Server VM i Azure Portal](virtual-machines-windows-sql-manage-portal.md).  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentations förbättringar

| Dokumentation | Information |
| --- | --- |
| **Ny hantering av virtuell SQL-portalwebbplats** | Om ett dussin artiklar har uppdaterats till den nya versionen av hanterings portalen för SQL VM. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>April 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Utöka stödet för SQL Server 2008/2008R2** | [Utöka stödet](virtual-machines-windows-sql-server-2008-eos-extend-support.md) för SQL Server 2008 och SQL Server 2008 R2 genom att migrera *i befintligt skick* till en virtuell Azure-dator. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Mars 2019

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Anpassad avbildnings support** | Nu kan du installera [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md#installation) för anpassade OS-och SQL-avbildningar, som erbjuder begränsade funktioner i [flexibel licensiering](virtual-machines-windows-sql-ahb.md). När du registrerar din anpassade avbildning med SQL-adressresursen anger du licens typen "AHUB", vilket annars Miss kan registreringen. | 
| **Stöd för namngiven instans** | Du kan nu använda [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md#installation) med en namngiven instans, om standard instansen har avinstallerats på rätt sätt. | 
| **Portal förbättring** | Azure Portals upplevelsen för att distribuera en SQL Server VM har förbättringar för att förbättra användbarhet. Mer information finns i [snabb start](quickstart-sql-vm-create-portal.md) och mer utförlig instruktions [](virtual-machines-windows-portal-sql-server-provision.md) guide för att distribuera en SQL Server VM.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Februari 2019

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Portal förbättring** | Nu kan du ändra licensierings modellen för en SQL Server VM från betala per användning för att få en egen licens med hjälp av [Azure Portal](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Installations förenkling för AG med Azure SQL VM CLI** | Nu är det enklare än någonsin att distribuera en tillgänglighets grupp till en SQL Server VM i Azure. Med [Azure SQL CLI för virtuella datorer](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) kan du skapa WSFC-, ILB-och AG-lyssnaren från kommando raden och i Record Time! Mer information finns i [använda Azure SQL VM CLI för att konfigurera Always on-tillgänglighetsgrupper för SQL Server på en virtuell Azure-dator](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>December 2018

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny resurs leverantör för SQL Cluster-grupp** | En ny resurs leverantör (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) som definierar metadata för Windows-redundansklustret. När du ansluter en SQL Server VM till *SqlVirtualMachineGroups* startar Windows redundansväxlingen Klustertjänst och ansluter den virtuella datorn till klustret.  |
|**Automatisera konfigurationen av en tillgänglighets grupps distribution med Azure snabb starts mallar** |Nu kan du skapa Windows-redundansklustret, ansluta SQL Server virtuella datorer till den, skapa lyssnaren och konfigurera interna Load Balancer med två Azure snabb starts mallar. Mer information finns i [använda Azure snabb starts mal len för att konfigurera Always on-tillgänglighetsgrupper för SQL Server på en virtuell Azure-dator](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatisk registrering av SQL VM-resurs leverantör** | SQL Server virtuella datorer som distribueras efter den här månaden registreras automatiskt med den nya SQL Server Resource providern. SQL Server virtuella datorer som distribuerats före den här månaden behöver fortfarande registreras manuellt. Mer information finns i [Registrera befintlig virtuell SQL-dator med en SQL VM-resurs leverantör](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Ny provider för SQL VM-resurs** |  En ny resurs leverantör för SQL Server virtuella datorer (Microsoft. SqlVirtualMachine) som ger bättre hantering av dina SQL Server VM. Mer information om hur du registrerar din virtuella dator finns i [Registrera befintlig virtuell SQL-dator med ny resurs leverantör](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Växla licensierings modell** | Nu kan du växla mellan den betalande per användning och hämta din egen licens modell för din virtuella SQL-dator med hjälp av Azure CLI eller PowerShell. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Ytterligare resurser

**Virtuella Windows-datorer**:

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en SQL Server virtuell Windows-dator](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Program mönster och utvecklings strategier för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQL Server på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en virtuell SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
