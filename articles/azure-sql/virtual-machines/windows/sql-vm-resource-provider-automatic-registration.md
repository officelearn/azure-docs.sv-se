---
title: Automatisk registrering med SQL VM Resource Provider
description: Lär dig hur du aktiverar funktionen för automatisk registrering för att automatiskt registrera alla tidigare och framtida SQL Server virtuella datorer med providern för SQL VM-resursen med hjälp av Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 23ecc3bdfb0ca85caf219fc262348937923f53c3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286130"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatisk registrering med SQL VM Resource Provider
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Aktivera funktionen för automatisk registrering i Azure Portal för att automatiskt registrera alla aktuella och framtida SQL Server på Azure-Virtual Machines (VM) med den virtuella SQL-resurs leverantören i Lightweight-läge. Vid registrering med den virtuella SQL VM-providern installeras [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md).

I den här artikeln lär du dig att aktivera funktionen för automatisk registrering. Alternativt kan du [Registrera en enskild virtuell dator](sql-vm-resource-provider-register.md)eller [Registrera dina virtuella datorer i bulk](sql-vm-resource-provider-bulk-register.md) med providern för SQL VM-resurs. 

## <a name="overview"></a>Översikt

När du registrerar din SQL Server VM med resurspoolen för SQL VM-providern installeras [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md). 

När automatisk registrering har Aktiver ATS körs ett jobb dagligen för att identifiera om SQL Server har installerats på alla oregistrerade virtuella datorer i prenumerationen. Det gör du genom att kopiera binärfilerna för SQL IaaS-agenttjänsten till den virtuella datorn och sedan köra ett engångs verktyg som söker efter registrerings data filen SQL Server. Om SQL Server Hive identifieras, registreras den virtuella datorn med [resurs leverantören för SQL-VM](sql-vm-resource-provider-register.md) i Lightweight-läge. Om det inte finns någon SQL Server Hive i registret tas binärfilerna bort.

När automatisk registrering har Aktiver ATS för en prenumeration registreras alla aktuella och framtida virtuella datorer som har SQL Server installerat med resurs leverantören för SQL-VM i lättviktigt läge. Du måste fortfarande [Uppgradera till fullständigt hanterings läge manuellt](sql-vm-resource-provider-register.md#upgrade-to-full) för att kunna dra nytta av den fullständiga funktions uppsättningen. 

> [!IMPORTANT]
> SQL IaaS agent-tillägget samlar in data i Express syfte att ge kunderna valfria förmåner när de använder SQL Server i Azure Virtual Machines. Microsoft kommer inte att använda dessa data för licens granskningar utan kundens medgivande. Se [SQL Server sekretess tillägg](/sql/sql-server/sql-server-privacy#non-personal-data) för mer information.

## <a name="prerequisites"></a>Krav

För att registrera SQL Server VM med resurs leverantören behöver du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En virtuell dator med Azure resurs modell [Windows Server 2008 R2 (eller senare)](../../../virtual-machines/windows/quick-create-portal.md) med [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) distribuerad till det offentliga molnet eller Azure Government molnet. Windows Server 2008 stöds inte. 


## <a name="enable"></a>Aktivera

Följ dessa steg om du vill aktivera automatisk registrering av SQL Server virtuella datorer i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till resurs sidan för [**virtuella SQL-datorer**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Välj **automatisk SQL Server VM registreringen** för att öppna sidan för **automatisk registrering** . 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Välj automatisk SQL Server VM registreringen för att öppna sidan för automatisk registrering":::

1. Välj din prenumeration i list rutan. 
1. Läs igenom villkoren och godkänn genom att välja **Jag accepterar**. 
1. Välj **Registrera** för att aktivera funktionen och registrera automatiskt alla aktuella och framtida SQL Server virtuella datorer med resurs leverantören för SQL-VM. Detta kommer inte att starta om SQL Server-tjänsten på någon av de virtuella datorerna. 

## <a name="disable"></a>Inaktivera

Använd [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/install-az-ps) för att inaktivera funktionen för automatisk registrering. När funktionen för automatisk registrering är inaktive rad måste SQL Server virtuella datorer som läggs till i prenumerationen registreras manuellt med providern för SQL VM-resurs. Det går inte att avregistrera befintliga SQL Server virtuella datorer som redan har registrerats.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill inaktivera automatisk registrering med Azure CLI kör du följande kommando: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Om du vill inaktivera automatisk registrering med Azure PowerShell kör du följande kommando: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Aktivera för flera prenumerationer

Du kan aktivera funktionen för automatisk registrering för flera Azure-prenumerationer med hjälp av PowerShell. 

Det gör du på följande sätt:

1. Spara [skriptet](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) till en `.ps1` fil, till exempel `EnableBySubscription.ps1` . 
1. Navigera till den plats där du sparade skriptet med hjälp av en administrativ kommando tolk eller PowerShell-fönster. 
1. Anslut till Azure ( `az login` ).
1. Kör skriptet och skicka i SubscriptionIds som parametrar som   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Exempel: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Misslyckade registrerings fel lagras i `RegistrationErrors.csv` samma katalog som du sparade och körde `.ps1` skriptet från. 

## <a name="next-steps"></a>Nästa steg

Uppgradera hanterbarhets läget till [full](sql-vm-resource-provider-register.md#upgrade-to-full) för att dra nytta av den fullständiga funktions uppsättningen som du fått av den virtuella SQL-providern för virtuella datorer. 
