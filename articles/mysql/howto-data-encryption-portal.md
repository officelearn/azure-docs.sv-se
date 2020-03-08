---
title: Data kryptering för Azure Database for MySQL med hjälp av Azure Portal
description: Lär dig hur du konfigurerar och hanterar data kryptering för dina Azure Database for MySQL med hjälp av Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 55c155dc4396672c02322c6c5727dac57d0ac8ef
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898734"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Data kryptering för Azure Database for MySQL med hjälp av Azure Portal

Lär dig hur du använder Azure Portal för att konfigurera och hantera data kryptering för din Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Krav för Azure CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* I Azure Key Vault skapar du ett nyckel valv och en nyckel som ska användas för en kundhanterad nyckel.
* Nyckel valvet måste ha följande egenskaper för att kunna användas som kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Rensa skyddad](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Nyckeln måste ha följande attribut för att användas som en kundhanterad nyckel:
  * Inget förfallo datum
  * Inte inaktiverat
  * Kan utföra get-, wrap-och unwrap Key-åtgärder

## <a name="set-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för viktiga åtgärder

1. I Key Vault väljer du **åtkomst principer** > **Lägg till åtkomst princip**.

   ![Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Välj **nyckel behörigheter**och välj **Hämta**, **Radbryt**, packa upp och **huvudobjektet**, vilket är namnet på **MySQL-servern**. Om ditt Server huvud namn inte finns i listan över befintliga huvud konton måste du registrera det. Du uppmanas att registrera ditt Server huvud namn när du försöker konfigurera data kryptering för första gången, och det Miss lyckas.

   ![Översikt över åtkomst princip](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Välj **Spara**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ange data kryptering för Azure Database for MySQL

1. I Azure Database for MySQL väljer du **data kryptering** för att ställa in den Kundhanterade nyckeln.

   ![Skärm bild av Azure Database for MySQL med data kryptering markerad](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Du kan antingen välja ett nyckel valv och nyckel par eller ange en nyckel identifierare.

   ![Skärm bild av Azure Database for MySQL med alternativ för data kryptering markerade](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Välj **Spara**.

4. Starta om servern för att se till att alla filer (inklusive temporära filer) är helt krypterade.

## <a name="restore-or-create-a-replica-of-the-server"></a>Återställa eller skapa en replik av servern

När Azure Database for MySQL har krypterats med en kunds hanterade nyckel som lagras i Key Vault krypteras även alla nyligen skapade kopior av servern. Du kan göra den här nya kopian antingen via en lokal åtgärd eller geo-återställning eller via en replikering (lokal/över region) åtgärd. För en krypterad MySQL-server kan du använda följande steg för att skapa en krypterad återställd Server.

1. Välj **översikt** > **återställning**på servern.

   ![Skärm bild av Azure Database for MySQL, med översikt och återställning markerat](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Om du använder en aktive rad server väljer du **replikering**under rubriken **Inställningar** .

   ![Skärm bild av Azure Database for MySQL med replikering markerad](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. När återställningen är klar är den nya servern som skapas krypterad med den primära serverns nyckel. Funktionerna och alternativen på servern är dock inaktiverade och servern är inte tillgänglig. Detta förhindrar all data manipulering eftersom den nya serverns identitet ännu inte har fått behörighet att komma åt nyckel valvet.

   ![Skärm bild av Azure Database for MySQL med otillgänglig status markerad](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Om du vill göra servern tillgänglig igen, verifierar du nyckeln på den återställda servern. Välj **data kryptering** > **omverifiera nyckel**.

   > [!NOTE]
   > Det första försöket att validera kommer att Miss lyckas eftersom den nya serverns tjänst huvud namn måste ges åtkomst till nyckel valvet. Om du vill generera tjänstens huvud namn väljer du **revalidate Key**, som visar ett fel, men som genererar tjänstens huvud namn. Därefter kan du se [de här stegen](#set-the-right-permissions-for-key-operations) tidigare i den här artikeln.

   ![Skärm bild av Azure Database for MySQL med omverifierings steg markerat](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Du måste ge nyckel valvet åtkomst till den nya servern.

4. När du har registrerat tjänstens huvud namn, verifierar nyckeln igen och servern fortsätter med sin normala funktion.

   ![Skärm bild av Azure Database for MySQL, med återställda funktioner](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Använda en Azure Resource Manager-mall för att aktivera data kryptering

Förutom Azure Portal kan du också aktivera data kryptering på din Azure Database for MySQL-server med Azure Resource Manager mallar för nya och befintliga servrar.

### <a name="for-a-new-server"></a>För en ny server

Använd en av de redan skapade Azure Resource Manager-mallarna för att etablera servern med data kryptering aktiverat: [exempel med data kryptering](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Den här Azure Resource Manager-mallen skapar en Azure Database for MySQL-server och använder nyckel **valvet** och **nyckeln** som skickas som parametrar för att aktivera data kryptering på servern.

### <a name="for-an-existing-server"></a>För en befintlig server
Du kan också använda Azure Resource Manager mallar för att aktivera data kryptering på befintliga Azure Database for MySQL-servrar.

* Skicka URI: n för den Azure Key Vault nyckel som du kopierade tidigare under egenskapen `keyVaultKeyUri` i egenskaps-objektet.

* Använd *2020-01-01 – för hands* version som API-version.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Nästa steg

 Mer information om data kryptering finns i [Azure Database for MySQL data kryptering med kundhanterad nyckel](concepts-data-encryption-mysql.md).
