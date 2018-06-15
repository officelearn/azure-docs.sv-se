---
title: Integrera Nyckelvalv med SQLServer på virtuella Windows-datorer i Azure (Resource Manager) | Microsoft Docs
description: Lär dig hur du kan automatisera konfigurationen av SQL Server-kryptering för användning med Azure Key Vault. Det här avsnittet beskriver hur du använder Azure Key Vault-integrering med SQL Server virtuella datorer som skapats med Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
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
ms.author: jroth
ms.openlocfilehash: 2b398f59aed1610825f495a6089990d393531305
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774734"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurera Azure Key Vault-integrering för SQLServer på virtuella Azure-datorer (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Översikt
Det finns flera funktioner för SQL Server-kryptering, t.ex [transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [kryptering på kolumnen (r)](https://msdn.microsoft.com/library/ms173744.aspx), och [säkerhetskopia av krypteringsnyckeln](https://msdn.microsoft.com/library/dn449489.aspx). Dessa typer av kryptering måste du hantera och lagra de kryptografiska nycklar som du använder för kryptering. Tjänsten Azure Key Vault (AKV) är utformade för att förbättra säkerhet och hantering av dessa nycklar i en säker och hög tillgänglighet på. Den [SQL Server-anslutningen](http://www.microsoft.com/download/details.aspx?id=45344) aktiverar SQL Server att använda de här nycklarna från Azure Key Vault.

Om du kör SQL Server med lokala datorer, finns [steg som du kan följa för att komma åt Azure Key Vault från din lokala SQL Server-datorn](https://msdn.microsoft.com/library/dn198405.aspx). Men för SQL Server på virtuella Azure-datorer, du kan spara tid genom att använda den *Azure Key Vault-integrering* funktion.

När den här funktionen aktiveras den automatiskt installerar SQL Server-anslutningen, konfigurerar EKM-providern för att komma åt Azure Key Vault och autentiseringsuppgifter så att du kan komma åt ditt valv. Om du tittar på stegen i ovanstående lokalt dokumentation, ser du att den här funktionen automatiserar steg 2 och 3. Det enda du behöver fortfarande göra manuellt är att skapa nyckelvalvet och nycklar. Därifrån är hela installationen av SQL-VM automatisk. När installationen har slutförts som den här funktionen, kan du köra T-SQL-uttryck för att börja kryptera dina databaser eller säkerhetskopieringar som vanligt.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Aktivera och konfigurera AKV-integreringen
Du kan aktivera AKV-integreringen under etableringen eller konfigurera den för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Om du etablerar en ny SQL Server-dator med Resource Manager ger ett sätt att aktivera Azure Key Vault-integrering med Azure-portalen. Azure Key Vault-funktionen är tillgänglig endast för Enterprise, Developer och Evaluation-utgåvor av SQL Server.

![SQL Azure Key Vault-integrering](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

En detaljerad genomgång av etablering finns [etablera en virtuell dator med SQL Server i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer
Välj den virtuella datorn för SQL Server för befintliga SQL Server virtuella datorer. Välj sedan den **SQL Server-konfigurationsfilen** avsnitt i den **inställningar** bladet.

![SQL AKV-integreringen för befintliga virtuella datorer](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

I den **SQL Server-konfigurationsfilen** bladet klickar du på den **redigera** i avsnittet automatisk Key Vault-integrering.

![Konfigurera SQL AKV-integreringen för befintliga virtuella datorer](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

När du är klar klickar du på den **OK** knappen längst ned på den **SQL Server-konfigurationsfilen** bladet för att spara dina ändringar.

> [!NOTE]
> Namn på autentiseringsuppgift som vi har skapat kommer att mappas till en SQL-inloggning senare. Detta gör att SQL-inloggning att få åtkomst till nyckelvalvet. 
>
>

> [!NOTE]
> Du kan också konfigurera AKV-integreringen med en mall. Mer information finns i [Azure quickstart-mall för Azure Key Vault-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

