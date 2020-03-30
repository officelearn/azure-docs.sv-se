---
title: Integrera nyckelvalv med SQL Server på virtuella Windows-datorer i Azure (Resource Manager) | Microsoft-dokument
description: Lär dig hur du automatiserar konfigurationen av SQL Server-kryptering för användning med Azure Key Vault. I det här avsnittet beskrivs hur du använder Azure Key Vault-integrering med virtuella SQL Server-datorer som skapats med Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102137"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Översikt
Det finns flera SQL Server-krypteringsfunktioner, till exempel [transparent datakryptering (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [kryptering på kolumnnivå (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)och [kryptering för säkerhetskopiering](https://msdn.microsoft.com/library/dn449489.aspx). Dessa krypteringsformer kräver att du hanterar och lagrar de kryptografiska nycklar som du använder för kryptering. Azure Key Vault (AKV) -tjänsten är utformad för att förbättra säkerheten och hanteringen av dessa nycklar på en säker och högtillgänglig plats. [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) gör det möjligt för SQL Server att använda dessa nycklar från Azure Key Vault.

Om du kör SQL Server med lokala datorer finns det [steg du kan följa för att komma åt Azure Key Vault från din lokala SQL Server-dator](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server i virtuella Azure-datorer kan du spara tid med hjälp av azure *key vault-integreringsfunktionen.*

När den här funktionen är aktiverad installeras SQL Server Connector automatiskt, EKM-providern konfigureras för åtkomst till Azure Key Vault och skapar autentiseringsuppgifterna så att du kan komma åt ditt valv. Om du har tittat på stegen i den tidigare nämnda lokala dokumentationen kan du se att den här funktionen automatiserar steg 2 och 3. Det enda du fortfarande skulle behöva göra manuellt är att skapa nyckeln valv och nycklar. Därifrån är hela installationen av din SQL VM automatiserad. När den här funktionen har slutfört den här installationen kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhetskopior som vanligt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM Provider version 1.0.4.0 installeras på SQL Server VM via [SQL IaaS-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Uppgradering av SQL IaaS-tillägget uppdaterar inte providerversionen. Överväg att uppgradera EKM-providerversionen manuellt om det behövs (till exempel när du migrerar till en SQL-hanterad instans).


## <a name="enabling-and-configuring-akv-integration"></a>Aktivera och konfigurera AKV-integrering
Du kan aktivera AKV-integrering under etablering eller konfigurera den för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Om du etablerar en ny virtuell SQL Server-dator med Resource Manager är Azure-portalen ett sätt att aktivera Azure Key Vault-integrering. Azure Key Vault-funktionen är endast tillgänglig för SQL Server-versioner av Företag, Utvecklare och Utvärdering.

![SQL Azure Key Vault-integrering](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

En detaljerad genomgång av etableringen finns i [Etablera en virtuell SQL Server-dator i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga virtuella SQL Server-datorer öppnar du [resursen virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) och väljer **Säkerhet** under **Inställningar**. Välj **Aktivera** om du vill aktivera Azure Key Vault-integrering. 

![SQL AKV-integrering för befintliga virtuella datorer](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

När du är klar väljer du knappen **Använd** längst ned på sidan **Säkerhet** för att spara ändringarna.

> [!NOTE]
> Det namn som vi skapade här mappas till en SQL-inloggning senare. Detta gör att SQL-inloggning för att komma åt nyckelvalvet. 


> [!NOTE]
> Du kan också konfigurera AKV-integrering med hjälp av en mall. Mer information finns i [Azure quickstart-mall för Azure Key Vault-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
