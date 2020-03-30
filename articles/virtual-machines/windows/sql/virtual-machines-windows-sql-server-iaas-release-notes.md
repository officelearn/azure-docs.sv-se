---
title: Dokumentationsändringar för SQL Server på virtuella Azure-datorer| Microsoft-dokument
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
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201653"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Dokumentationsändringar för SQL Server på virtuella Azure-datorer

Med Azure kan du distribuera en virtuell dator (VM) med en inbyggd avbildning av SQL Server. Den här artikeln sammanfattar de dokumentationsändringar som är associerade med nya funktioner och förbättringar i de senaste versionerna av [SQL Server på Virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Januari 2020

| Ändringar | Information |
| --- | --- |
| **Support för Azure Government** | Det är nu möjligt att registrera virtuella SQL Server-datorer med SQL VM-resursprovidern för virtuella datorer som finns i [Azure Government-molnet.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Ändringar | Information |
 --- | --- |
| **Gratis DR-replik i Azure** | Du kan vara värd för en [kostnadsfri passiv instans](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) för haveriberedskap i Azure för din lokala SQL Server-instans om du har Software [Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registrering av bulkresursprovider** | Du kan nu [massregistrera](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) virtuella SQL-datorer med resursprovidern. | 
|**Prestandaoptimerad lagringskonfiguration** | Du kan nu [anpassa lagringskonfigurationen helt](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) när du skapar en ny VIRTUELL SQL Server. |
|**Premium filresurs för FCI** | Nu kan du skapa en redundansklusterin med hjälp av en [Premium-filresurs](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) i stället för den ursprungliga metoden [för Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Azure-dedikerad värd** | Du kan köra din virtuella SQL Server-dator på en [Azure Dedicated Host](virtual-machines-windows-sql-dedicated-host.md). | 
| **Flytta SQL VM till en annan region** | Använd Azure Site Recovery för att [migrera din VIRTUELLA SQL Server-dator från en region till en annan](virtual-machines-windows-sql-move-different-region.md). |
|  **Nya installationslägen för SQL IaaS** | Det är nu möjligt att installera SQL Server IaaS-tillägget i [lättläge](virtual-machines-windows-sql-server-agent-extension.md) för att undvika att starta om SQL Server-tjänsten.  |
| **Ändring av SQL Server-utgåva** | Du kan nu ändra [egenskapen edition](virtual-machines-windows-sql-change-edition.md) för din VIRTUELLA SQL Server.You can now change the edition property for your SQL Server VM. |
| **Ändringar i SQL VM-resursprovidern** | Du kan [registrera din VIRTUELLA SQL Server-dator med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) med hjälp av de nya SQL IaaS-lägena. Den här funktionen omfattar [Windows Server 2008-avbildningar.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Ta med egna licensavbildningar med Azure Hybrid Benefit** | Ta med egna licensavbildningar som distribueras från Azure Marketplace kan nu byta sin [licenstyp till användningsbaserad betalning](virtual-machines-windows-sql-ahb.md#remarks).| 
| **Ny SQL Server VM-hantering i Azure-portalen** | Det finns nu ett sätt att hantera din VIRTUELLA SQL Server-dator i Azure-portalen. Mer information finns [i Hantera virtuella SQL Server-datorer i Azure-portalen](virtual-machines-windows-sql-manage-portal.md).  | 
| **Utökat stöd för SQL Server 2008/2008 R2** | [Utöka stödet](virtual-machines-windows-sql-server-2008-eos-extend-support.md) för SQL Server 2008 och SQL Server 2008 R2 genom att migrera *som är* till en Azure VM. | 
| **Anpassad bildstödbarhet** | Du kan nu installera [SQL Server IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md#installation) till anpassade OS- och SQL-avbildningar, vilket ger den begränsade funktionaliteten hos [flexibel licensiering](virtual-machines-windows-sql-ahb.md). När du registrerar din anpassade avbildning med SQL-resursprovidern anger du licenstypen som "AHUB". Annars misslyckas registreringen. | 
| **Namngiven instansstödbarhet** | Du kan nu använda [SQL Server IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md#installation) med en namngiven instans, om standardinstansen har avinstallerats korrekt. | 
| **Förbättringar av portalen** | Azure-portalens upplevelse för distribution av en VIRTUELL SQL Server har moderniserats för att förbättra användbarheten. Mer information finns i den korta [snabbstarten](quickstart-sql-vm-create-portal.md) och mer ingående [hur du kan distribuera](virtual-machines-windows-portal-sql-server-provision.md) en virtuell SQL Server-dator.|
| **Portal förbättring** | Det är nu möjligt att ändra licensieringsmodellen för en VIRTUELL SQL Server från användningsbaserad betalning för att ta med din egen licens med hjälp av [Azure-portalen](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider).|
| **Förenkling av distribution av tillgänglighetsgrupp med Azure SQL Server VM CLI** | Det är nu enklare än någonsin att distribuera en tillgänglighetsgrupp till en VIRTUELL SQL Server i Azure. Du kan använda [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) för att skapa Windows redundanskluster, intern belastningsutjämnare och tillgänglighet gruppavlyssnare alla från kommandoraden. Mer information finns i [Använda Azure SQL Server VM CLI för att konfigurera en alltid på tillgänglighetsgrupp för SQL Server på en Virtuell Azure.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Ändringar | Information |
| --- | --- |
|  **Ny resursprovider för ett SQL Server-kluster** | En ny resursleverantör (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definierar metadata för Windows redundansklustret. Ansluta till en VIRTUELL SQL Server till *SqlVirtualMachineGroups* startar WSFC-tjänsten (Windows Server Failover Cluster) och ansluter den virtuella datorn till klustret.  |
| **Automatisk installation av en distribution av tillgänglighetsgrupper med Snabbstartsmallar för Azure** |Det är nu möjligt att skapa Windows redundanskluster, ansluta SQL Server virtuella datorer till den, skapa lyssnaren och konfigurera den interna belastningsutjämnaren med två Azure-snabbstartsmallar. Mer information finns i [Använda Azure-snabbstartsmallar för att konfigurera en alltid på tillgänglighetsgrupp för SQL Server på en Virtuell Azure.](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 
| **Automatisk registrering till SQL VM-resursprovidern** | Virtuella SQL Server-datorer som distribueras efter den här månaden registreras automatiskt hos den nya SQL Server-resursprovidern. Virtuella SQL Server-datorer som distribuerats före den här månaden måste fortfarande registreras manuellt. Mer information finns i [Registrera en virtuell SQL Server-dator i Azure med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Ny RESURSPROVIDER FÖR SQL VM** |  En ny resursleverantör (Microsoft.SqlVirtualMachine) ger bättre hantering av virtuella SQL Server-datorer. Mer information om hur du registrerar dina virtuella datorer finns [i Registrera en virtuell SQL Server-dator i Azure med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Växla licensieringsmodell** | Du kan nu växla mellan pay-per-usage och bring-your-own-license-modeller för din VIRTUELLA SQL Server-dator med hjälp av Azure CLI eller PowerShell. Mer information finns i [Så här ändrar du licensieringsmodellen för en virtuell SQL Server-dator i Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Ytterligare resurser

**Virtuella datorer i Windows:**

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Etablera en VIRTUELL SQL Server Windows-dator](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och haveriberedskap för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-high-availability-dr.md)
* [Metodtips för prestanda för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md)
* [Programmönster och utvecklingsstrategier för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer:**

* [Översikt över SQL Server på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en virtuell SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server på Linux-dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
