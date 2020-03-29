---
title: Konfigurera kundhanterade nycklar för ditt Azure Cosmos DB-konto
description: Lär dig hur du konfigurerar kundhanterade nycklar för ditt Azure Cosmos DB-konto med Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 6e2a90b8f81b9b945905ee98beb1686c54a62e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063767"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurera kundhanterade nycklar för ditt Azure Cosmos-konto med Azure Key Vault

> [!NOTE]
> För närvarande måste du begära åtkomst för att kunna använda den här funktionen. För att göra [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)det, vänligen kontakta .

Data som lagras i ditt Azure Cosmos-konto krypteras automatiskt och sömlöst med nycklar som hanteras av Microsoft (**tjänsthanterade nycklar**). Du kan också välja att lägga till ett andra lager kryptering med nycklar som du hanterar (**kundhanterade nycklar).**

![Lager av kryptering runt kunddata](./media/how-to-setup-cmk/cmk-intro.png)

Du måste lagra kundhanterade nycklar i [Azure Key Vault](../key-vault/key-vault-overview.md) och ange en nyckel för varje Azure Cosmos-konto som är aktiverat med kundhanterade nycklar. Den här nyckeln används för att kryptera alla data som lagras i det kontot.

> [!NOTE]
> För närvarande är kundhanterade nycklar endast tillgängliga för nya Azure Cosmos-konton. Du bör konfigurera dem när du skapar kontot.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registrera Azure Cosmos DB-resursprovidern för din Azure-prenumeration

1. Logga in på [Azure-portalen,](https://portal.azure.com/)gå till din Azure-prenumeration och välj **Resursleverantörer** under fliken **Inställningar:**

   !["Resursleverantörer" från den vänstra menyn](./media/how-to-setup-cmk/portal-rp.png)

1. Sök efter resursleverantören **Microsoft.DocumentDB.** Kontrollera om resursleverantören redan har markerats som registrerad. Om inte, välj resursprovidern och välj **Registrera:**

   ![Registrera resursprovidern Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurera din Azure Key Vault-instans

Om du använder kundhanterade nycklar med Azure Cosmos DB måste du ange två egenskaper i Azure Key Vault-instansen som du planerar att använda för att vara värd för dina krypteringsnycklar. Dessa egenskaper inkluderar **Mjuk borttagning** och **Rensa inte**. Dessa egenskaper är inte aktiverade som standard. Du kan aktivera dem med hjälp av antingen PowerShell eller Azure CLI.

Mer information om hur du aktiverar dessa egenskaper i en befintlig Azure Key Vault-instans finns i avsnitten "Aktivera mjuk borttagning" och "Aktivera rensningsskydd" i någon av följande artiklar:

- [Så här använder du mjuk borttagning med PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Så här använder du mjuk borttagning med Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Lägga till en åtkomstprincip i azure key vault-instansen

1. Från Azure-portalen går du till Azure Key Vault-instansen som du planerar att använda för att vara värd för dina krypteringsnycklar. Välj **Åtkomstprinciper** på den vänstra menyn:

   !["Åtkomstprinciper" från den vänstra menyn](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Välj **+ Lägg till åtkomstprincip**.

1. Under listrutan **Nyckelbehörigheter** väljer du **Hämta,** **Ta bort nyckel**och **Radbryt** nyckelbehörigheter:

   ![Välja rätt behörigheter](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Under **Välj huvudnamn**väljer du **Ingen markerad**. Sök sedan efter **Azure Cosmos** DB-huvudnamn och välj det (för att göra `a232010e-820c-4083-83bb-3ace5fc29d0b` det lättare att hitta kan du också `57506a73-e302-42a9-b869-6f12d9ec29e9`söka efter huvud-ID: för alla Azure-regioner utom Azure Government-regioner där huvud-ID är ). Slutligen väljer **du Välj** längst ned. Om **Azure Cosmos** DB-huvudnumret inte finns med i listan kan du behöva registrera om resursleverantören **Microsoft.DocumentDB** enligt beskrivningen i avsnittet [Registrera resursprovidern](#register-resource-provider) i den här artikeln.

   ![Välj azure Cosmos DB-huvudnamn](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Välj **Lägg till** om du vill lägga till den nya åtkomstprincipen.

## <a name="generate-a-key-in-azure-key-vault"></a>Generera en nyckel i Azure Key Vault

1. Gå till Azure Key Vault-instansen som du planerar att använda för att vara värd för dina krypteringsnycklar från Azure-portalen. Välj sedan **Tangenter** på den vänstra menyn:

   !["Nycklar" från den vänstra menyn](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Välj **Generera/importera**, ange ett namn på den nya nyckeln och välj en RSA-nyckelstorlek. Minst 3072 rekommenderas för bästa säkerhet. Välj sedan **Skapa:**

   ![Skapa en ny nyckel](./media/how-to-setup-cmk/portal-akv-gen.png)

1. När nyckeln har skapats väljer du den nyskapade nyckeln och sedan dess aktuella version.

1. Kopiera nyckelns **nyckelidentifierare**, förutom delen efter det sista snedstrecket:

   ![Kopiera nyckelns nyckelidentifierare](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Skapa ett nytt Azure Cosmos-konto

### <a name="using-the-azure-portal"></a>Använda Azure Portal

När du skapar ett nytt Azure Cosmos DB-konto från Azure-portalen väljer du **Kundhanterad nyckel** i **krypteringssteget.** I fältet **Nyckel-URI** klistrar du in URI/nyckelidentifieraren för azure key vault-nyckeln som du kopierade från föregående steg:

![Ställa in CMK-parametrar i Azure-portalen](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

När du skapar ett nytt Azure Cosmos DB-konto med PowerShell:

- Skicka URI-värden för Azure Key Vault-nyckeln som kopierats tidigare under egenskapen **keyVaultKeyUri** i **PropertyObject**.

- Använd **2019-12-12** som API-version.

> [!IMPORTANT]
> Du måste `Location` ange parametern uttryckligen för att kontot ska kunna skapas med kundhanterade nycklar.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager-mall

När du skapar ett nytt Azure Cosmos-konto via en Azure Resource Manager-mall:

- Skicka URI-värden för Azure Key Vault-nyckeln som du kopierade tidigare under egenskapen **keyVaultKeyUri** i **egenskapsobjektet.**

- Använd **2019-12-12** som API-version.

> [!IMPORTANT]
> Du måste `Location` ange parametern uttryckligen för att kontot ska kunna skapas med kundhanterade nycklar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Distribuera mallen med följande PowerShell-skript:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>Använda Azure CLI

När du skapar ett nytt Azure Cosmos-konto via Azure CLI skickar du URI-värden för Azure Key Vault-nyckeln som du kopierade tidigare under parametern **--key-uri.**

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Finns det någon extra kostnad för att använda kundhanterade nycklar?

Ja. För att ta hänsyn till den ytterligare beräkningsbelastning som krävs för att hantera datakryptering och dekryptering med kundhanterade nycklar, förbrukar alla åtgärder som körs mot Azure Cosmos-kontot en 25-procentig ökning av [begärandeenheter](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Vilka data krypteras med de kundhanterade nycklarna?

Alla data som lagras i ditt Azure Cosmos-konto krypteras med de kundhanterade nycklarna, förutom följande metadata:

- Namnen på dina Azure Cosmos [DB-konton, databaser och behållare](./account-overview.md#elements-in-an-azure-cosmos-account)

- Namnen på dina [lagrade procedurer](./stored-procedures-triggers-udfs.md)

- Egenskapsvägarna som deklarerats i [indexeringsprinciperna](./index-policy.md)

- Värdena för behållarnas [partitionsnycklar](./partitioning-overview.md)

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Stöds kundhanterade nycklar för befintliga Azure Cosmos-konton?

Den här funktionen är för närvarande endast tillgänglig för nya konton.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Finns det en plan för att stödja finare granularitet än nycklar på kontonivå?

Inte för närvarande, men nycklar på behållarnivå övervägs.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Hur påverkar kundhanterade nycklar en säkerhetskopia?

Azure Cosmos DB tar [regelbundna och automatiska säkerhetskopior](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) av data som lagras i ditt konto. Den här åtgärden säkerhetskopierar krypterade data. Om du vill använda den återställda säkerhetskopian krävs krypteringsnyckeln som du använde vid tidpunkten för säkerhetskopieringen. Detta innebär att ingen återkallelse gjordes och den version av nyckeln som användes vid tidpunkten för säkerhetskopieringen kommer fortfarande att aktiveras.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hur återkallar jag en krypteringsnyckel?

Nyckelåterkallning görs genom att inaktivera den senaste versionen av nyckeln:

![Inaktivera en nyckelversion](./media/how-to-setup-cmk/portal-akv-rev2.png)

Om du vill återkalla alla nycklar från en Azure Key Vault-instans kan du också ta bort åtkomstprincipen som beviljats Azure Cosmos DB-huvudmannen:

![Ta bort åtkomstprincipen för Azure Cosmos DB-huvudprincipen](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Vilka åtgärder är tillgängliga när en kundhanterad nyckel har återkallats?

Den enda möjliga åtgärden när krypteringsnyckeln har återkallats är borttagning av konton.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [datakryptering i Azure Cosmos DB](./database-encryption-at-rest.md).
- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md).
