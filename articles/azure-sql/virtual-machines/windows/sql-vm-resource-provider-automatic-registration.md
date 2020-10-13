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
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996887"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatisk registrering med SQL VM Resource Provider
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Aktivera funktionen för automatisk registrering i Azure Portal att automatiskt registrera alla aktuella och framtida SQL Server på virtuella Azure-datorer med resurs leverantören för SQL-VM i lättviktigt läge.

I den här artikeln lär du dig att aktivera funktionen för automatisk registrering. Alternativt kan du [Registrera en enskild virtuell dator](sql-vm-resource-provider-register.md)eller [Registrera dina virtuella datorer i bulk](sql-vm-resource-provider-bulk-register.md) med providern för SQL VM-resurs. 

## <a name="overview"></a>Översikt

Med [resurs leverantören för SQL-VM](sql-vm-resource-provider-register.md#overview) kan du hantera dina SQL Server VM från Azure Portal. Dessutom möjliggör resurs leverantören en robust funktions uppsättning, inklusive [Automatisk uppdatering](automated-patching.md), [Automatisk säkerhets kopiering](automated-backup.md), samt övervakning och hanterings funktioner. Den låser också upp flexibiliteten för [licensiering](licensing-model-azure-hybrid-benefit-ahb-change.md) och [utgåvor](change-sql-server-edition.md) . Tidigare var dessa funktioner bara tillgängliga för SQL Server VM avbildningar som distribueras från Azure Marketplace. 

Funktionen för automatisk registrering gör det möjligt för kunder att automatiskt registrera alla aktuella och framtida SQL Server virtuella datorer i sin Azure-prenumeration med providern för SQL VM-resurs. Detta skiljer sig från manuell registrering, som bara fokuserar på aktuella SQL Server virtuella datorer. 

Automatisk registrering registrerar SQL Server virtuella datorer i Lightweight-läge. Du måste fortfarande [Uppgradera till fullständigt hanterings läge manuellt](sql-vm-resource-provider-register.md#upgrade-to-full) för att kunna dra nytta av den fullständiga funktions uppsättningen. 

## <a name="prerequisites"></a>Förutsättningar

För att registrera SQL Server VM med resurs leverantören behöver du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [virtuell Windows-dator](../../../virtual-machines/windows/quick-create-portal.md) i Azure Resource Model med [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) distribuerad till det offentliga molnet eller Azure Government molnet. 


## <a name="enable"></a>Aktivera

Följ stegen nedan om du vill aktivera automatisk registrering av SQL Server virtuella datorer i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

   Till exempel: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Misslyckade registrerings fel lagras i `RegistrationErrors.csv` samma katalog som du sparade och körde `.ps1` skriptet från. 

## <a name="next-steps"></a>Nästa steg

Uppgradera hanterbarhets läget till [full](sql-vm-resource-provider-register.md#upgrade-to-full) för att dra nytta av den fullständiga funktions uppsättningen som du fått av den virtuella SQL-providern för virtuella datorer. 
