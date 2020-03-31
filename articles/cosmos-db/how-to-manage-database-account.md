---
title: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
description: Lär dig hur du hanterar Azure Cosmos DB-resurser med hjälp av azure-portal-, PowerShell-, CLI- och Azure Resource Manager-mallarna
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247389"
---
# <a name="manage-an-azure-cosmos-account"></a>Hantera ett Azure Cosmos-konto

Den här artikeln beskriver hur du hanterar olika uppgifter i ett Azure Cosmos-konto med hjälp av Azure-portalen, Azure PowerShell, Azure CLI och Azure Resource Manager-mallar.

## <a name="create-an-account"></a>Skapa ett konto

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure-portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Se [Skapa ett Azure Cosmos DB-konto med Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Se [Skapa ett Azure Cosmos DB-konto med Powershell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager-mall

Den här Azure Resource Manager-mallen skapar ett Azure Cosmos-konto för SQL API som konfigurerats med två regioner och alternativ för att välja konsekvensnivå, automatisk redundans och multi-master. Om du vill distribuera den här mallen klickar du på Distribuera till Azure på lässidan, [Skapa Azure Cosmos-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Lägga till/ta bort regioner från ditt databaskonto

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure-portal

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till ditt Azure Cosmos-konto och öppna menyn **Replikera data globalt.**

1. Om du vill lägga till regioner markerar **+** du hexagonerna på kartan med etiketten som motsvarar önskade regioner. Om du vill lägga till en region väljer du alternativet **+ Lägg till region** och väljer en region på den nedrullningsbara menyn.

1. Om du vill ta bort regioner avmarkerar du en eller flera regioner från kartan genom att välja de blå sexhörningarna med kryssmarkeringar. Eller välj ”papperskorgsikonen” (🗑) intill regionen på höger sida.

1. Spara ändringarna genom att välja **OK**.

   ![Lägga till eller ta bort regionsmenyn](./media/how-to-manage-database-account/add-region.png)

I skrivläge för en enstaka region kan du inte ta bort skrivregionen. Du måste redundansväxla till en annan region innan du kan ta bort den aktuella skrivregionen.

I skrivläge för flera regioner kan du lägga till eller ta bort vilka regioner som helst förutsatt att du har minst en region.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Se [Lägga till eller ta bort regioner med Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Se [Lägga till eller ta bort regioner med Powershell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurera flera skrivregioner

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure-portal

Öppna fliken **Replikera data globalt** och välj **Aktivera** för att aktivera skrivningar med flera regioner. När du har aktiverat skrivningar med flera regioner läsregioner lässer och skrivs alla lästa regioner som du för närvarande har för kontot.

![Azure Cosmos-konto konfigurerar skärmbild av flera mallar](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Se [Aktivera regioner med flera skrivningar med Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Se [Aktivera flerskrivningsregioner med Powershell](manage-with-powershell.md#multi-master)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Mall för Resurshanteraren

Ett konto kan migreras från enstängt till multi-master genom att distribuera `enableMultipleWriteLocations: true`resource manager-mallen som används för att skapa kontot och inställningen . Följande Azure Resource Manager-mall är en minimal mall som kommer att distribuera ett Azure Cosmos-konto för SQL API med två regioner och flera skrivplatser aktiverade.

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

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Aktivera automatisk redundans för ditt Azure Cosmos-konto

Alternativet Automatisk redundans gör att Azure Cosmos DB kan redundans till den region som har högst redundansprioritet utan användaråtgärd om en region blir otillgänglig. När automatisk redundans är aktiverat kan regionprioriteten ändras. Kontot måste ha två eller flera regioner för att aktivera automatisk redundans.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure-portal

1. Öppna fönstret **Replikera data globalt** från ditt Azure Cosmos-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**. 

4. Välj **Spara**.

   ![Menyn Automatisk redundans i portalen](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Se [Aktivera automatisk redundans med Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Se [Aktivera automatisk redundans med Powershell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ange redundansprioritet för ditt Azure Cosmos-konto

När ett Cosmos-konto har konfigurerats för automatisk redundans kan redundansprioriteten för regioner ändras.

> [!IMPORTANT]
> Du kan inte ändra skrivregionen (redundansprioritet noll) när kontot har konfigurerats för automatisk redundans. Om du vill ändra skrivområdet måste du inaktivera automatisk redundans och göra en manuell redundans.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure-portal

1. Öppna fönstret **Replikera data globalt** från ditt Azure Cosmos-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**.

4. Du ändrar redundansprioritet genom att dra läsregionerna via de tre punkterna till vänster om raden som visas när du hovrar över dem.

5. Välj **Spara**.

   ![Menyn Automatisk redundans i portalen](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Se [Ange redundansprioritet med Azure CLI](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Se [Ange redundansprioritet med Powershell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Utför manuell redundansväxling för ett Azure Cosmos-konto

> [!IMPORTANT]
> Azure Cosmos-kontot måste konfigureras för manuell redundans för att den här åtgärden ska lyckas.

Processen för att utföra en manuell redundans innebär att ändra kontots skrivregion (redundansprioritet = 0) till en annan region som konfigurerats för kontot.

> [!NOTE]
> Multi-master-konton kan inte skickas över manuellt. För program som använder Azure Cosmos SDK identifierar SDK när en region blir otillgänglig och omdirigeras sedan automatiskt till nästa närmaste region om du använder API för flera målningar i SDK.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure-portal

1. Gå till ditt Azure Cosmos-konto och öppna menyn **Replikera data globalt.**

2. Längst upp på menyn väljer du **Manuell redundans**.

   ![Menyn Replikera data globalt](./media/how-to-manage-database-account/replicate-data-globally.png)

3. På menyn **Manuell redundans** väljer du din nya skrivregion. Markera kryssrutan för att bekräfta att du förstår att det här alternativet ändrar din skrivregion.

4. Utlös redundansväxlingen genom att välja **OK**.

   ![Menyn Manuell redundans i portalen](./media/how-to-manage-database-account/manual-failover.png)

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Se [Utlösa manuell redundans med Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Se [Utlösa manuell redundans med Powershell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du hanterar Azure Cosmos-kontot samt databas och behållare finns i följande artiklar:

* [Hantera Azure Cosmos DB med Azure PowerShell](manage-with-powershell.md)
* [Hantera Azure Cosmos DB med Azure CLI](manage-with-cli.md)
