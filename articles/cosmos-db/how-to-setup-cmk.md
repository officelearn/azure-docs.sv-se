---
title: Konfigurera Kundhanterade nycklar för ditt Azure Cosmos DB-konto
description: Lär dig hur du konfigurerar Kundhanterade nycklar för ditt Azure Cosmos DB-konto
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911868"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Konfigurera Kundhanterade nycklar för ditt Azure Cosmos DB-konto

> [!NOTE]
> För tillfället måste du begära åtkomst för att använda den här funktionen. Om du vill göra det kontaktar du [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Data som lagras i ditt Azure Cosmos DB-konto är automatiskt och sömlöst krypterade. Azure Cosmos DB erbjuder två alternativ för att hantera nycklar som används för att kryptera dina data i vila:
- **Tjänstens hanterade nycklar**. Som standard hanterar Microsoft nycklarna som används för att kryptera ditt Azure Cosmos DB-konto.
- **Kundhanterade nycklar (CMK)** . Du kan välja att lägga till ett andra lager av kryptering med nycklar som du hanterar.

Kundhanterade nycklar måste lagras i [Azure Key Vault](../key-vault/key-vault-overview.md). En nyckel måste anges för varje CMK-aktiverat konto och används för att kryptera alla data som lagras i kontot.

## <a name="setup"></a>Installation

För närvarande är Kundhanterade nycklar bara tillgängliga för nya konton och måste konfigureras när kontot skapas.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. kontrol lera att Azure Cosmos DB Resource provider har registrerats för din Azure-prenumeration

Från Azure Portal går du till din Azure-prenumeration och väljer **resurs leverantörer** på den vänstra menyn:

!["Resource providers"-post från den vänstra menyn](./media/how-to-setup-cmk/portal-rp.png)

Sök efter **Microsoft. DocumentDB** -resurs leverantören.
- Om resurs leverantören redan har marker ATS som registrerad behöver du inte göra något.
- Om inte väljer du den och klickar på **Registrera**:

    ![Registrera Microsoft. DocumentDB Resource Provider](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. Konfigurera Azure Key Vault-instansen

Om du använder Kundhanterade nycklar med Azure Cosmos DB måste två egenskaper anges på den Azure Key Vault instans som du planerar att använda som värd för dina krypterings nycklar: **mjuk borttagning** och **Rensa inte**. De här egenskaperna är inte aktiverade som standard men kan aktive ras med antingen PowerShell eller Azure CLI.

Information om hur du aktiverar de här egenskaperna på en befintlig Azure Key Vault instans finns i avsnitten med rubriken aktivera mjuk borttagning och aktivera rensnings skydd i någon av följande artiklar:
- [Använda mjuk borttagning med PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Använda mjuk borttagning med Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. Lägg till en åtkomst princip i Azure Key Vault-instansen

Från Azure Portal går du till den Azure Key Vault instans som du planerar att använda som värd för dina krypterings nycklar. Välj sedan **åtkomst principer** på den vänstra menyn:

!["Åtkomst principer" på den vänstra menyn](./media/how-to-setup-cmk/portal-akv-ap.png)

- Välj **+ Lägg till åtkomst princip**
- Under List rutan **nyckel behörigheter** väljer du **Hämta**och **packa**upp **nyckel** -och omslutning:

    ![Välja rätt behörigheter](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- Under **Välj huvud konto**väljer du **ingen vald**. Sök sedan efter och välj **Azure Cosmos DB** -huvudobjektet. Klicka slutligen på **Välj** längst ned (om **Azure Cosmos DB** -huvudobjektet inte hittas kan du behöva registrera om **Microsoft. DocumentDB** Resource Provider i steg 1):

    ![Välja Azure Cosmos DB huvud konto](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Välj **Lägg** till för att lägga till den nya åtkomst principen

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. generera en nyckel i Azure Key Vault

Från Azure Portal väljer du den Azure Key Vault instans som du planerar att använda som värd för dina krypterings nycklar. Välj sedan **nycklar** i den vänstra menyn:

!["Nycklar"-post från den vänstra menyn](./media/how-to-setup-cmk/portal-akv-keys.png)

- Välj **generera/importera**
- Ange ett namn för den nya nyckeln, Välj en nyckel storlek för RSA (minst 3072 rekommenderas för bästa säkerhet) och välj **skapa**:

    ![Skapa en ny nyckel](./media/how-to-setup-cmk/portal-akv-gen.png)

- När nyckeln har skapats klickar du på den nyligen skapade nyckeln och sedan på den aktuella versionen
- Kopiera nyckelns **nyckel identifierare** förutom delen efter det sista snedstrecket:

    ![Nyckelns nyckel identifierare kopieras](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. skapa ett nytt Azure Cosmos DB konto

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

När du skapar ett nytt Azure Cosmos DB konto från Azure Portal väljer du **kundhanterad nyckel** i **krypterings** steget. I fältet **nyckel-URI** skickar du URI: n för den Azure Key Vault nyckel som kopierats från steg 4:

![Ange CMK-parametrar i Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>Använda PowerShell

När du skapar ett nytt Azure Cosmos DB-konto med PowerShell
- Skicka URI: n för den Azure Key Vault nyckel som kopierats från steg 4 under egenskapen **keyVaultKeyUri** i **PropertyObject**,
- Se till att använda "2019-12-12" som API-version.

> [!IMPORTANT]
> Parametern `Location` måste anges explicit för att kontot ska kunna skapas med CMK.

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

#### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager mallar

När du skapar ett nytt Azure Cosmos DB konto via en Azure Resource Manager-mall:
- Skicka URI: n för den Azure Key Vault nyckel som kopierats från steg 4 under egenskapen **keyVaultKeyUri** i objektet **Egenskaper**
- Se till att använda "2019-12-12" som API-version

> [!IMPORTANT]
> Parametern `location` måste anges explicit för att kontot ska kunna skapas med CMK.

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

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Finns det ytterligare kostnader när du använder Kundhanterade nycklar?

Ja. För att kunna ta hänsyn till den ytterligare beräknings belastning som krävs för att hantera data kryptering och dekryptering med Kundhanterade nycklar får alla åtgärder som utförs mot Azure Cosmos DB-kontot en ökning på 25% i enheter för förbrukade [enheter](./request-units.md) .

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Vilka data krypteras med CMK?

Alla data som lagras i ditt Azure Cosmos DB-konto krypteras med CMK, förutom följande meta-data:
- namnen på dina Azure Cosmos DB [-konton, databaser och behållare](./account-overview.md#elements-in-an-azure-cosmos-account),
- namnen på dina [lagrade procedurer](./stored-procedures-triggers-udfs.md),
- egenskaps Sök vägarna som deklareras i dina [indexerings principer](./index-policy.md)
- värdena för din behållar [partitionsnyckel](./partitioning-overview.md).

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Stöds kund hanterade nycklar för befintliga konton?

Den här funktionen är för närvarande endast tillgänglig för nya konton.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Finns det någon plan för att ge bättre granularitet än nycklar på konto nivå?

För närvarande beaktas inte nycklar på container nivå.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Hur påverkar kund hanterade nycklar säkerhets kopior?

Azure Cosmos DB använder [regelbundna och automatiska säkerhets kopieringar](./online-backup-and-restore.md) av de data som lagras i ditt konto. Den här åtgärden säkerhetskopierar krypterade data. För att en återställd säkerhets kopiering ska kunna användas måste krypterings nyckeln som används vid tidpunkten för säkerhets kopieringen fortfarande vara tillgänglig. Detta innebär att ingen åter kallelse har gjorts och den version av nyckeln som användes vid tidpunkten för säkerhets kopieringen fortfarande är aktive rad.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hur gör jag för att återkalla en krypterings nyckel?

Nyckel återkallning görs genom att den senaste versionen av nyckeln inaktive ras:

![Inaktivera en nyckels version](./media/how-to-setup-cmk/portal-akv-rev2.png)

Om du vill återkalla alla nycklar från en Azure Key Vault-instans kan du också ta bort åtkomst principen som beviljats till Azure Cosmos DB-huvudobjektet:

![Tar bort åtkomst principen för Azure Cosmos DB-huvudobjektet](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Vilka åtgärder är tillgängliga när en kundhanterad nyckel har återkallats?

Den enda åtgärd som är möjlig när krypterings nyckeln har återkallats är borttagning av kontot.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [data kryptering i Azure Cosmos DB](./database-encryption-at-rest.md)
- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md)