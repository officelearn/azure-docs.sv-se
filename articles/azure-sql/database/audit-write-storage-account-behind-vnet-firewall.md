---
title: Granska till lagrings konto bakom VNet och brand vägg
description: Konfigurera granskning för att skriva databas händelser på ett lagrings konto bakom virtuellt nätverk och brand vägg
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: cf615da14e2749f998f900500cc33da68a625e16
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041649"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Skriv granskning till ett lagrings konto bakom VNet och brand vägg
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Granskning för [Azure SQL Database](sql-database-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) har stöd för skrivning av databas händelser till ett [Azure Storage konto](../../storage/common/storage-account-overview.md) bakom ett virtuellt nätverk och en brand vägg.

I den här artikeln beskrivs två sätt att konfigurera Azure SQL Database och Azure Storage-kontot för det här alternativet. Det första använder Azure Portal, den andra använder REST.

## <a name="background"></a>Bakgrund

[Azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) är det grundläggande Bygg blocket för ditt privata nätverk i Azure. VNet möjliggör många typer av Azure-resurser, till exempel Azure Virtual Machines (VM), för att på ett säkert sätt kommunicera med varandra, Internet och lokala nätverk. VNet liknar ett traditionellt nätverk i ditt eget Data Center, men ger ytterligare fördelar med Azure-infrastruktur som skalning, tillgänglighet och isolering.

Om du vill veta mer om VNet-begreppen, bästa praxis och många fler, se [Vad är Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

Mer information om hur du skapar ett virtuellt nätverk finns i [snabb start: skapa ett virtuellt nätverk med hjälp av Azure Portal](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Förutsättningar

Följande förutsättningar måste vara uppfyllda för att granskning ska kunna skrivas till ett lagrings konto bakom ett VNet eller en brand vägg:

> [!div class="checklist"]
>
> * Ett allmänt-syfte v2-lagrings konto. Om du har ett allmänt v1-eller Blob Storage-konto [uppgraderar du till ett allmänt lagrings konto](../../storage/common/storage-account-upgrade.md). Mer information finns i [typer av lagrings konton](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Lagrings kontot måste finnas i samma prenumeration och på samma plats som den [logiska SQL-servern](logical-servers.md).
> * Det Azure Storage kontot kräver `Allow trusted Microsoft services to access this storage account` . Ange detta på lagrings kontots **brand väggar och virtuella nätverk**.
> * Du måste ha `Microsoft.Authorization/roleAssignments/write` behörighet för det valda lagrings kontot. Mer information finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurera i Azure Portal

Anslut till [Azure Portal](https://portal.azure.com) med din prenumeration. Navigera till resurs gruppen och servern.

1. Klicka på **granskning** under säkerhets rubriken. Välj **på**.

2. Välj **lagring**. Välj det lagrings konto där loggarna ska sparas. Lagrings kontot måste uppfylla kraven som [anges i krav](#prerequisites).

3. **Information om öppen lagring**

  > [!NOTE]
  > Om det valda lagrings kontot ligger bakom VNet visas följande meddelande:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Om du inte ser det här meddelandet finns inte lagrings kontot bakom ett VNet.

4. Välj antalet dagar för kvarhållningsperioden. Klicka sedan på **OK**. Loggar som är äldre än kvarhållningsperioden tas bort.

5. Välj **Spara** på gransknings inställningarna.

Du har konfigurerat granskning att skriva till ett lagrings konto bakom ett VNet eller en brand vägg.

## <a name="configure-with-rest-commands"></a>Konfigurera med REST-kommandon

Som ett alternativ till att använda Azure Portal kan du använda REST-kommandon för att konfigurera granskning för att skriva databas händelser på ett lagrings konto bakom ett VNet och en brand vägg.

Exempel skripten i det här avsnittet kräver att du uppdaterar skriptet innan du kör dem. Ersätt följande värden i skripten:

|Exempelvärde|Exempel Beskrivning|
|:-----|:-----|
|`<subscriptionId>`| ID för Azure-prenumeration|
|`<resource group>`| Resursgrupp|
|`<logical SQL server>`| servernamn|
|`<administrator login>`| Administratörskonto |
|`<complex password>`| Komplext lösen ord för administratörs kontot|

Konfigurera SQL audit för att skriva händelser till ett lagrings konto bakom ett VNet eller en brand vägg:

1. Registrera servern med Azure Active Directory (Azure AD). Använd antingen PowerShell eller REST API.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Exempel förfrågan

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Begärandetext

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Öppna [Azure-portalen](https://portal.azure.com). Navigera till ditt lagringskonto. Leta upp **Access Control (IAM)** och klicka på **Lägg till roll tilldelning**. Tilldela RBAC-rollen **Storage BLOB data Contributor** till den server som är värd för den databas som du registrerade med Azure Active Directory (Azure AD) som i föregående steg.

   > [!NOTE]
   > Endast medlemmar med ägar behörighet kan utföra det här steget. För olika inbyggda roller för Azure-resurser, se [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md).

3. Konfigurera [serverns BLOB Auditing-princip](/rest/api/sql/server%20auditing%20settings/createorupdate)utan att ange en *storageAccountAccessKey*:

   Exempel förfrågan

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Begärandetext

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Använda Azure PowerShell

- [Skapa eller uppdatera databas gransknings princip (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Skapa eller uppdatera Server gransknings princip (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="next-steps"></a>Nästa steg

* [Använd PowerShell för att skapa en tjänst slut punkt för virtuellt nätverk och sedan en regel för virtuella nätverk för Azure SQL Database.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Virtual Network regler: åtgärder med REST API: er](/rest/api/sql/virtualnetworkrules)
* [Använd tjänst slut punkter och regler för virtuella nätverk för servrar](vnet-service-endpoint-rule-overview.md)
