---
title: Integrera Key Vault med SQLServer på Windows virtuella datorer i Azure (Resource Manager) | Microsoft Docs
description: Lär dig att automatisera konfigurationen av SQL Server-kryptering för användning med Azure Key Vault. Det här avsnittet förklarar hur du använder Azure Key Vault-integrering med SQL Server-datorer som skapas med Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 13d698cfbc0241248a77fd5f3b148a9393320c64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076033"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurera Azure Key Vault-integrering för SQLServer på Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Översikt
Det finns flera funktioner för SQL Server-kryptering, till exempel [transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [kolumnen filnivåkryptering (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), och [kryptering](https://msdn.microsoft.com/library/dn449489.aspx). Dessa typer av kryptering måste du hantera och lagra de kryptografiska nycklarna som du använder för kryptering. Tjänsten Azure Key Vault (AKV) är utformad för att förbättra säkerhet och hantering av de här nycklarna på en säker och mycket tillgänglig plats. Den [SQL Server-anslutningen](https://www.microsoft.com/download/details.aspx?id=45344) gör det möjligt för SQL Server att använda de här nycklarna från Azure Key Vault.

Om du kör SQL Server med lokala datorer, finns [steg som du kan följa för att få åtkomst till Azure Key Vault från en lokal SQL Server-dator](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server i virtuella Azure-datorer, kan du spara tid genom att använda den *Azure Key Vault-integrering* funktionen.

När den här funktionen aktiveras den automatiskt installerar SQL Server-anslutningen, konfigurerar EKM-providern för att komma åt Azure Key Vault och autentiseringsuppgifter så att du kan komma åt ditt valv. Du kan se att den här funktionen automatiserar steg 2 och 3 om du har tittat på stegen i den tidigare nämnda lokala-dokumentationen. Det enda du behöver fortfarande göra manuellt är att skapa nyckelvalvet och nycklar. Därifrån kan är hela konfigurationen av din SQL-VM automatisk. När den här funktionen har slutfört den här konfigurationen, kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhetskopior som vanligt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Version 1.0.4.0 i EKM-providern är installerad på SQL Server-dator via den [SQL IaaS-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Providerversionen uppdateras inte om du uppgraderar SQL IaaS-tillägget. Funderar du manuellt uppgradera EKM-providerversion om det behövs (till exempel när du migrerar till en SQL-hanterad instans).


## <a name="enabling-and-configuring-akv-integration"></a>Aktivera och konfigurera AKV-integreringen
Du kan aktivera AKV-integreringen under etableringen eller konfigurera den för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Om du etablerar en ny SQL Server-dator med Resource Manager tillhandahåller ett sätt att aktivera Azure Key Vault-integrering i Azure-portalen. Azure Key Vault-funktionen är tillgänglig endast för Enterprise, Developer och Evaluation-utgåvor av SQL Server.

![SQL Azure Key Vault-integrering](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

En detaljerad genomgång för att etablera finns i [etablera en virtuell dator med SQL Server i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Befintliga SQL Server-datorer, öppna ditt [SQL VM-resurs](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) och välj **Security** under **inställningar**. Välj **aktivera** att aktivera Azure Key Vault-integrering. 

![SQL AKV-integreringen för befintliga virtuella datorer](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

När du är klar, Välj den **tillämpa** knappen längst ned på den **Security** sidan för att spara dina ändringar.

> [!NOTE]
> Namn på autentiseringsuppgift som vi har skapat kommer att mappas till en SQL-inloggning senare. På så sätt kan SQL-inloggning att få åtkomst till nyckelvalvet. 


> [!NOTE]
> Du kan också konfigurera AKV-integreringen med en mall. Mer information finns i [Azure-snabbstartsmall för Azure Key Vault-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
