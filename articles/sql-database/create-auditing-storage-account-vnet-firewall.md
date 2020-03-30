---
title: Granska till lagringskonto bakom VNet och brandvägg
description: Konfigurera granskning för att skriva databashändelser på ett lagringskonto bakom virtuellt nätverk och brandvägg
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387639"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Skriva granskning till ett lagringskonto bakom VNet och brandvägg

Granskning för [Azure SQL Database](sql-database-technical-overview.md) och Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stöder att skriva databashändelser till ett Azure [Storage-konto](../storage/common/storage-account-overview.md) bakom ett virtuellt nätverk och en brandvägg. 

I den här artikeln beskrivs två sätt att konfigurera Azure SQL Server och Azure-lagringskonto för det här alternativet. Den första använder Azure-portalen, den andra använder REST.

### <a name="background"></a>Bakgrund

[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) är den grundläggande byggstenen för ditt privata nätverk i Azure. VNet gör det möjligt för många typer av Azure-resurser, till exempel Virtuella Azure-datorer (VM), att kommunicera säkert med varandra, internet och lokala nätverk. VNet liknar ett traditionellt nätverk i ditt eget datacenter, men medför ytterligare fördelar med Azure-infrastruktur som skala, tillgänglighet och isolering.

Mer information om VNet-begreppen, metodtips och många fler finns i [Vad är Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Mer information om hur du skapar ett virtuellt nätverk finns i [Snabbstart: Skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Krav

För att granskning ska kunna skriva till ett lagringskonto bakom ett virtuella nätverk eller en brandvägg krävs följande förutsättningar:

> [!div class="checklist"]
> * Ett allmänt v2-lagringskonto. Om du har ett allmänt v1- eller blob-lagringskonto [uppgraderar du till ett allmänt v2-lagringskonto](../storage/common/storage-account-upgrade.md). Mer information finns i [Typer av lagringskonton](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Lagringskontot måste finnas på samma prenumeration och på samma plats som Azure SQL Database-servern. 
> * Azure Storage-kontot `Allow trusted Microsoft services to access this storage account`kräver . Ställ in detta på brandväggar för **lagringskonto och virtuella nätverk**.
> * Du måste `Microsoft.Authorization/roleAssignments/write` ha behörighet för det valda lagringskontot. Mer information finns [i Azure-inbyggda roller](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurera i Azure Portal

Anslut till [Azure-portalen](https://portal.azure.com) med din prenumeration. Navigera till resursgruppen och Azure SQL-databasservern.

1. Klicka på **Granskning** under rubriken Säkerhet. Välj **På**.

2. Välj **Lagring**. Välj det lagringskonto där loggar ska sparas. Lagringskontot måste uppfylla kraven i [Förutsättningar](#prerequisites).

3. Öppna **lagringsinformation** 

  > [!NOTE]
  > Om det valda lagringskontot ligger bakom virtuella nätverk visas följande meddelande:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Om du inte ser det här meddelandet ligger lagringskontot inte bakom ett virtuella nätverk.

4. Välj antalet dagar för kvarhållningsperioden. Klicka sedan på **OK**. Loggar som är äldre än kvarhållningsperioden tas bort.

5. Välj **Spara** på granskningsinställningarna.

Du har konfigurerat granskningen för att skriva till ett lagringskonto bakom ett virtuella nätverk eller en brandvägg. 

## <a name="configure-with-rest-commands"></a>Konfigurera med REST-kommandon

Som ett alternativ till att använda Azure-portalen kan du använda REST-kommandon för att konfigurera granskning för att skriva databashändelser på ett lagringskonto bakom ett virtuella nätverk och brandvägg. 

Exempelskripten i det här avsnittet kräver att du uppdaterar skriptet innan du kör dem. Ersätt följande värden i skripten:

|Exempelvärde|Exempel på beskrivning|
|:-----|:-----|
|`<subscriptionId>`| Azure-prenumerations-ID|
|`<resource group>`| Resursgrupp|
|`<sql database server>`| Namn på Azure SQL-databasserver|
|`<administrator login>`| Administratörskonto för SQL-databas |
|`<complex password>`| Komplext lösenord för administratörskontot|

Så här konfigurerar du SQL Audit för att skriva händelser till ett lagringskonto bakom ett virtuella nätverk eller en brandvägg:

1. Registrera din Azure SQL Database-server med Azure Active Directory (Azure AD). Använd antingen PowerShell- eller REST API.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Exempel på begäran

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

2. Öppna [Azure-portalen](https://portal.azure.com). Navigera till ditt lagringskonto. Leta reda på **Access Control (IAM)** och klicka på **Lägg till rolltilldelning**. Tilldela **RBAC-rollen för storage blob data contributor** till din Azure SQL Server som är värd för din Azure SQL-databas som du registrerade med Azure Active Directory (Azure AD) som i föregående steg.

   > [!NOTE]
   > Endast medlemmar med ägarbehörighet kan utföra det här steget. För olika inbyggda roller för Azure-resurser, se [Azure inbyggda roller](../role-based-access-control/built-in-roles.md).

3. Konfigurera [Azure SQL-serverns blob-granskningsprincip](/rest/api/sql/server%20auditing%20settings/createorupdate), utan att ange en *storageAccountAccessKey:*

   Exempel på begäran

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

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att skapa en slutpunkt för virtuella nätverkstjänster och sedan en virtuell nätverksregel för Azure SQL Database.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Regler för virtuella nätverk: Åtgärder med REST-API:er](/rest/api/sql/virtualnetworkrules)
- [Använda slutpunkter och regler för virtuella nätverkstjänster för databasservrar](sql-database-vnet-service-endpoint-rule-overview.md)
