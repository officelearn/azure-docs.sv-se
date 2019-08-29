---
title: Integrera Key Vault med SQL Server på virtuella Windows-datorer i Azure (Resource Manager) | Microsoft Docs
description: Lär dig hur du automatiserar konfigurationen av SQL Server kryptering för användning med Azure Key Vault. I det här avsnittet beskrivs hur du använder Azure Key Vault-integrering med SQL Server virtuella datorer som skapats med Resource Manager.
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102137"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurera Azure Key Vault-integrering för SQL Server på Azure-Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Översikt
Det finns flera SQL Server krypterings funktioner, till exempel [transparent data kryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE (Column Level Encryption)](https://msdn.microsoft.com/library/ms173744.aspx)och [kryptering av säkerhets kopior](https://msdn.microsoft.com/library/dn449489.aspx). Dessa krypterings former kräver att du hanterar och lagrar de kryptografiska nycklar som du använder för kryptering. Tjänsten Azure Key Vault (AKV) är utformad för att förbättra säkerheten och hanteringen av dessa nycklar på en säker plats med hög tillgänglighet. Med [SQL Server-anslutning](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server använda nycklarna från Azure Key Vault.

Om du kör SQL Server med lokala datorer finns det [steg du kan följa för att få åtkomst till Azure Key Vault från din lokala SQL Server dator](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server i virtuella Azure-datorer kan du spara tid genom att använda funktionen för *Azure Key Vault-integrering* .

När den här funktionen är aktive rad installerar den automatiskt SQL Server-anslutning, konfigurerar EKM-providern för att få åtkomst till Azure Key Vault och skapar autentiseringsuppgiften så att du kan komma åt ditt valv. Om du har tittat på stegen i den tidigare nämnda dokumentationen kan du se att den här funktionen automatiserar steg 2 och 3. Det enda du behöver göra är att skapa nyckel valvet och nycklarna manuellt. Därifrån kommer hela installationen av den virtuella SQL-datorn att automatiseras. När den här funktionen har slutfört den här installationen kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhets kopior på vanligt sätt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM-providerns version 1.0.4.0 är installerad på SQL Server VM via [SQL IaaS-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Uppgradering av SQL IaaS-tillägget kommer inte att uppdatera providerns version. Överväg att uppgradera EKM-providerns version manuellt om det behövs (till exempel vid migrering till en SQL-hanterad instans).


## <a name="enabling-and-configuring-akv-integration"></a>Aktivera och konfigurera AKV-integrering
Du kan aktivera AKV-integrering under etableringen eller konfigurera den för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Om du skapar en ny SQL Server virtuell dator med Resource Manager, är Azure Portal ett sätt att aktivera Azure Key Vault integrering. Azure Key Vault funktionen är bara tillgänglig för SQL Server för Enterprise, Developer och Evaluation.

![SQL Azure Key Vault-integrering](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

En detaljerad genom gång av etableringen finns i [etablera en SQL Server virtuell dator i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer öppnar du [resursen SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) och väljer **säkerhet** under **Inställningar**. Välj **Aktivera** för att aktivera Azure Key Vault-integrering. 

![SQL AKV-integrering för befintliga virtuella datorer](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

När du är färdig väljer du knappen **tillämpa** längst ned på sidan **säkerhet** för att spara ändringarna.

> [!NOTE]
> Namnet på autentiseringsuppgifterna som vi skapade här kommer att mappas till en SQL-inloggning senare. Detta gör att SQL-inloggningen kan komma åt nyckel valvet. 


> [!NOTE]
> Du kan också konfigurera AKV-integrering med en mall. Mer information finns i [Azure snabb starts mal len för Azure Key Vault-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
