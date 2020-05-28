---
title: Dokumentations ändringar för SQL Server på Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om de nya funktionerna och förbättringarna för SQL Server på en virtuell Azure-dator
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046000"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Dokumentations ändringar för SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Med Azure kan du distribuera en virtuell dator (VM) med en avbildning av SQL Server inbyggd. Den här artikeln sammanfattar dokumentations ändringarna som är associerade med nya funktioner och förbättringar i de senaste versionerna av [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Januari 2020

| Något | Information |
| --- | --- |
| **Azure Government support** | Nu kan du registrera SQL Server virtuella datorer med resurs leverantören för SQL VM för virtuella datorer som finns i [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) molnet. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Något | Information |
 --- | --- |
| **Kostnads fri DR-replikering i Azure** | Du kan vara värd för en [kostnads fri passiv instans](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) för haveri beredskap i Azure för din lokala SQL Server instans om du har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registrering av Mass resurs leverantör** | Nu kan du [Mass registrera](sql-vm-resource-provider-bulk-register.md) virtuella SQL-datorer med resurs leverantören. | 
|**Prestandaoptimerad lagrings konfiguration** | Du kan nu [helt anpassa lagrings konfigurationen](storage-configuration.md#new-vms) när du skapar en ny SQL Server VM. |
|**Premium-filresurs för FCI** | Nu kan du skapa en instans av ett kluster för växling vid fel med en [Premium-filresurs](failover-cluster-instance-premium-file-share-manually-configure.md) i stället för den ursprungliga [Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md)-metoden. 
| **Dedikerad Azure-värd** | Du kan köra SQL Server VM på en [dedikerad Azure-värd](dedicated-host.md). | 
| **Flytta SQL VM till en annan region** | Använd Azure Site Recovery för att [migrera SQL Server VM från en region till en annan](move-sql-vm-different-region.md). |
|  **Nya installations lägen för SQL-IaaS** | Nu är det möjligt att installera SQL Server IaaS-tillägget i [lättviktigt läge](sql-server-iaas-agent-extension-automate-management.md) för att undvika att starta om tjänsten SQL Server.  |
| **Ändring av SQL Servers utgåva** | Nu kan du ändra [egenskapen version](change-sql-server-edition.md) för SQL Server VM. |
| **Ändringar av providern för SQL VM-resurs** | Du kan [Registrera din SQL Server VM med den virtuella SQL](sql-vm-resource-provider-register.md) -IaaS med hjälp av de nya SQL-lägena. Den här funktionen omfattar [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) -avbildningar.|
| **Hämta egna licens bilder med hjälp av Azure Hybrid-förmån** | Med en egen licens avbildning som distribueras från Azure Marketplace kan du nu byta [licens typ till betala per](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)användning.| 
| **Ny SQL Server VM hantering i Azure Portal** | Det finns nu ett sätt att hantera dina SQL Server VM i Azure Portal. Mer information finns i [hantera SQL Server virtuella datorer i Azure Portal](manage-sql-vm-portal.md).  | 
| **Utökat stöd för SQL Server 2008/2008 R2** | [Utöka stödet](sql-server-2008-extend-end-of-support.md) för SQL Server 2008 och SQL Server 2008 R2 genom att migrera *som* en virtuell Azure-dator. | 
| **Anpassad avbildnings support** | Nu kan du installera [SQL Server IaaS-tillägget](sql-server-iaas-agent-extension-automate-management.md#installation) för anpassade OS-och SQL-avbildningar, som erbjuder begränsade funktioner i [flexibel licensiering](licensing-model-azure-hybrid-benefit-ahb-change.md). När du registrerar din anpassade avbildning med SQL-adressresursen anger du licens typen "AHUB". Annars Miss kommer registreringen. | 
| **Stöd för namngiven instans** | Du kan nu använda [tillägget SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) med en namngiven instans, om standard instansen har avinstallerats på rätt sätt. | 
| **Portal förbättring** | Azure Portals upplevelsen för att distribuera en SQL Server VM har förbättringar för att förbättra användbarhet. Mer information finns i [snabb start](sql-vm-create-portal-quickstart.md) och mer utförlig [instruktions guide](create-sql-vm-portal.md) för att distribuera en SQL Server VM.|
| **Portal förbättring** | Det är nu möjligt att ändra licensierings modellen för en SQL Server VM från betala per användning för att få en egen licens genom att använda [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Förenkling av tillgänglighets grupps distribution med Azure SQL Server VM CLI** | Nu är det enklare än någonsin att distribuera en tillgänglighets grupp till en SQL Server VM i Azure. Du kan använda [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) för att skapa Windows-redundanskluster, en intern belastningsutjämnare och tillgänglighets grupps lyssnare från kommando raden. Mer information finns i [använda Azure SQL Server VM CLI för att konfigurera en Always on-tillgänglighets grupp för SQL Server på en virtuell Azure-dator](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Något | Information |
| --- | --- |
|  **Ny resurs leverantör för ett SQL Server kluster** | En ny Resource Provider (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definierar metadata för Windows-redundansklustret. När du ansluter en SQL Server VM till *SqlVirtualMachineGroups* startar du WSFC-tjänsten (Windows Server failover Cluster) och ansluter den virtuella datorn till klustret.  |
| **Automatiserad installation av en tillgänglighets grupps distribution med Azure snabb starts mallar** |Nu kan du skapa Windows-redundansklustret, ansluta SQL Server virtuella datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren med två Azure snabb starts mallar. Mer information finns i [använda Azures snabb starts mallar för att konfigurera en tillgänglighets grupp som alltid är tillgänglig för SQL Server på en virtuell Azure-dator](availability-group-quickstart-template-configure.md). | 
| **Automatisk registrering till resurs leverantören för SQL-VM** | SQL Server virtuella datorer som distribueras efter den här månaden registreras automatiskt med den nya SQL Server Resource providern. SQL Server virtuella datorer som distribuerats före den här månaden behöver registreras manuellt. Mer information finns i [Registrera en SQL Server virtuell dator i Azure med providern för SQL VM-resurs](sql-vm-resource-provider-register.md).|
|**Ny provider för SQL VM-resurs** |  En ny Resource Provider (Microsoft. SqlVirtualMachine) ger bättre hantering av dina SQL Server virtuella datorer. Mer information om hur du registrerar dina virtuella datorer finns i [Registrera en SQL Server virtuell dator i Azure med providern för SQL VM-resurs](sql-vm-resource-provider-register.md). |
|**Växla licensierings modell** | Nu kan du växla mellan modellerna betala per användning och hämta egna licenser för din SQL Server VM med hjälp av Azure CLI eller PowerShell. Mer information finns i [så här ändrar du licensierings modellen för en SQL Server virtuell dator i Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Ytterligare resurser

**Virtuella Windows-datorer**:

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Etablera en SQL Server virtuell Windows-dator](create-sql-vm-portal.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](migrate-to-vm-from-sql-server.md)
* [Hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Metod tips för prestanda för SQL Server på Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Program mönster och utvecklings strategier för SQL Server på Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQL Server på en virtuell Linux-dator](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Etablera en virtuell SQL Server Linux-dator](../linux/sql-vm-create-portal-quickstart.md)
* [Vanliga frågor och svar (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server på Linux dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
