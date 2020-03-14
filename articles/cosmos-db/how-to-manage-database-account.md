---
title: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
description: Lär dig hur du hanterar Azure Cosmos DB-resurser med hjälp av mallarna Azure Portal, PowerShell, CLI och Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247389"
---
# <a name="manage-an-azure-cosmos-account"></a>Hantera ett Azure Cosmos-konto

Den här artikeln beskriver hur du hanterar olika uppgifter i ett Azure Cosmos-konto med hjälp av Azure-portalen, Azure PowerShell, Azure CLI och Azure Resource Manager-mallar.

## <a name="create-an-account"></a>Skapa ett konto

### <a id="create-database-account-via-portal"></a>Azure-portalen

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

Information om hur du [skapar ett Azure Cosmos DB konto med Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Information om hur du [skapar ett Azure Cosmos DB konto med PowerShell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager mall

Den här Azure Resource Manager-mallen skapar ett Azure Cosmos-konto för SQL-API som kon figurer ATS med två regioner och alternativ för att välja konsekvens nivå, automatisk redundans och flera huvud servrar. Om du vill distribuera den här mallen klickar du på distribuera till Azure på sidan viktigt och [skapar ett Azure Cosmos-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Lägga till/ta bort regioner från ditt databaskonto

### <a id="add-remove-regions-via-portal"></a>Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till ditt Azure Cosmos-konto och öppna menyn **replikera data globalt** .

1. Om du vill lägga till regioner väljer du sexhörningarna på kartan med **+** etiketten som motsvarar din önskade region (er). Om du vill lägga till en region väljer du alternativet **+ Lägg till region** och väljer en region i den nedrullningsbara menyn.

1. Om du vill ta bort regioner avmarkerar du en eller flera regioner från kartan genom att välja de blå sexhörningarna med kryssmarkeringar. Eller välj ”papperskorgsikonen” (🗑) intill regionen på höger sida.

1. Spara ändringarna genom att välja **OK**.

   ![Lägga till eller ta bort regionsmenyn](./media/how-to-manage-database-account/add-region.png)

I skrivläge för en enstaka region kan du inte ta bort skrivregionen. Du måste redundansväxla till en annan region innan du kan ta bort den aktuella skrivregionen.

I skrivläge för flera regioner kan du lägga till eller ta bort vilka regioner som helst förutsatt att du har minst en region.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

Se [lägga till eller ta bort regioner med Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Se [lägga till eller ta bort regioner med PowerShell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Konfigurera flera Skriv-regioner

### <a id="configure-multiple-write-regions-portal"></a>Azure-portalen

Öppna fliken **replikera data globalt** och välj **Aktivera** för att aktivera flera regioner. När du har aktiverat skrivningar i flera regioner blir alla Läs regioner som du för närvarande har på kontot Läs-och skriv regioner.

![Azure Cosmos-konto konfigurerar skärm bild med flera huvud servrar](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

Se [aktivera flera-Write-regioner med Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Se [aktivera flera-Write-regioner med PowerShell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-mall

Ett konto kan migreras från en huvud server till flera Masters genom att distribuera Resource Manager-mallen som används för att skapa kontot och ange `enableMultipleWriteLocations: true`. Följande Azure Resource Manager mall är en minimal mall som används för att distribuera ett Azure Cosmos-konto för SQL-API med två regioner och flera Skriv platser är aktiverade.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Aktivera automatisk redundans för ditt Azure Cosmos-konto

Med alternativet automatisk redundans kan Azure Cosmos DB redundansväxla till den region som har den högsta prioriteten för redundans utan användar åtgärd om en region blir otillgänglig. När automatisk redundans har Aktiver ATS kan du ändra region prioriteten. Kontot måste ha två eller flera regioner för att aktivera automatisk redundans.

### <a id="enable-automatic-failover-via-portal"></a>Azure-portalen

1. Öppna fönstret **replikera data globalt** från ditt Azure Cosmos-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**. 

4. Välj **Spara**.

   ![Menyn Automatisk redundans i portalen](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

Läs [Aktivera automatisk redundans med Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Läs [Aktivera automatisk redundans med PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ange redundansprioritet för ditt Azure Cosmos-konto

När ett Cosmos-konto har kon figurer ATS för automatisk redundans kan växlings prioriteten för regioner ändras.

> [!IMPORTANT]
> Du kan inte ändra Skriv regionen (växlings prioriteten är noll) när kontot har kon figurer ATS för automatisk redundans. Om du vill ändra Skriv regionen måste du inaktivera automatisk redundans och göra en manuell redundansväxling.

### <a id="set-failover-priorities-via-portal"></a>Azure-portalen

1. Öppna fönstret **replikera data globalt** från ditt Azure Cosmos-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**.

4. Du ändrar redundansprioritet genom att dra läsregionerna via de tre punkterna till vänster om raden som visas när du hovrar över dem.

5. Välj **Spara**.

   ![Menyn Automatisk redundans i portalen](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

Se [ange prioritet för redundans med Azure CLI](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Se [ange prioritet för redundans med PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Utföra manuell redundans på ett Azure Cosmos-konto

> [!IMPORTANT]
> Azure Cosmos-kontot måste konfigureras för manuell redundansväxling för att åtgärden ska lyckas.

Processen för att utföra en manuell redundansväxling innebär att ändra kontots Skriv region (redundans prioritet = 0) till en annan region som kon figurer ATS för kontot.

> [!NOTE]
> Flera huvud konton kan inte växlas över manuellt. För program som använder Azure Cosmos SDK identifierar SDK när en region blir otillgänglig och dirigerar sedan automatiskt till nästa närmaste region om du använder API för flera värdar i SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure-portalen

1. Gå till ditt Azure Cosmos-konto och öppna menyn **replikera data globalt** .

2. Längst upp på menyn väljer du **Manuell redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. På menyn **Manuell redundans** väljer du din nya skrivregion. Markera kryssrutan för att bekräfta att du förstår att det här alternativet ändrar din skrivregion.

4. Utlös redundansväxlingen genom att välja **OK**.

   ![Menyn Manuell redundans i portalen](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

Se [Utlös manuell redundans med Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Se [Utlös manuell redundans med PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du hanterar Azure Cosmos-kontot samt databas och behållare finns i följande artiklar:

* [Hantera Azure Cosmos DB med Azure PowerShell](manage-with-powershell.md)
* [Hantera Azure Cosmos DB med Azure CLI](manage-with-cli.md)
