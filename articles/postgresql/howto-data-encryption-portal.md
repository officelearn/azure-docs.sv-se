---
title: Datakryptering - Azure-portal - för Azure Database för PostgreSQL - Enkel server
description: Lär dig hur du konfigurerar och hanterar datakryptering för din Azure-databas för PostgreSQL Single server med hjälp av Azure-portalen.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297075"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Datakryptering för Azure Database för PostgreSQL Single server med hjälp av Azure-portalen

Lär dig hur du använder Azure-portalen för att konfigurera och hantera datakryptering för din Azure-databas för PostgreSQL Single server.

## <a name="prerequisites-for-azure-cli"></a>Förutsättningar för Azure CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* Skapa ett nyckelvalv och nyckel som ska användas för en kundhanterad nyckel i Azure Key Vault.
* Nyckelvalvet måste ha följande egenskaper som ska användas som kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Rensa skyddad](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Nyckeln måste ha följande attribut att använda som kundhanterad nyckel:
  * Inget utgångsdatum
  * Inte inaktiverad
  * Kunna utföra get, wrap nyckel och packa upp viktiga operationer

## <a name="set-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för nyckelåtgärder

1. I Key Vault väljer du **Åtkomstprincip** > **Lägg till åtkomstprincip**.

   ![Skärmbild av Key Vault, med åtkomstprinciper och Lägg till åtkomstprincip markerad](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Välj **Nyckelbehörigheter**och välj **Hämta**, **Radbryta**, **Ta bort**och **huvudmannen**, som är namnet på PostgreSQL-servern. Om serverns huvudnamn inte finns i listan över befintliga huvudnamn måste du registrera det. Du uppmanas att registrera serverns huvudnamn när du försöker konfigurera datakryptering för första gången, och det misslyckas.  

   ![Översikt över åtkomstprinciper](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Välj **Spara**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ange datakryptering för Azure Database för PostgreSQL Single server

1. I Azure Database for PostgreSQL väljer du **Datakryptering** för att konfigurera den kundhanterade nyckeln.

   ![Skärmbild av Azure Database för PostgreSQL, med datakryptering markerad](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Du kan antingen välja ett nyckelvalv och nyckelpar eller ange en nyckelidentifierare.

   ![Skärmbild av Azure Database för PostgreSQL, med datakrypteringsalternativ markerade](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Välj **Spara**.

4. Starta om servern om du vill vara säkra på att alla filer (inklusive temporära filer) är helt krypterade.

## <a name="restore-or-create-a-replica-of-the-server"></a>Återställa eller skapa en replik av servern

När Azure Database for PostgreSQL Single server krypteras med en kunds hanterade nyckel lagrad i Key Vault krypteras även alla nyskapade kopior av servern. Du kan göra den nya kopian antingen via en lokal eller geoåterställningsåtgärd eller genom en replik (lokal/regionöverskridande åtgärd). Så för en krypterad PostgreSQL-server kan du använda följande steg för att skapa en krypterad återställd server.

1. Välj > **Översiktsåterställning**på servern . **Overview**

   ![Skärmbild av Azure Database för PostgreSQL, med översikt och återställning markerad](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Eller för en replikeringsaktiverad server väljer du **Replikering**under rubriken **Inställningar** .

   ![Skärmbild av Azure Database för PostgreSQL, med replikering markerad](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. När återställningen är klar krypteras den nya servern som skapas med den primära serverns nyckel. Funktionerna och alternativen på servern är dock inaktiverade och servern är otillgänglig. Detta förhindrar datamanipulering, eftersom den nya serverns identitet ännu inte har fått behörighet att komma åt nyckelvalvet.

   ![Skärmbild av Azure Database för PostgreSQL, med otillgänglig status markerad](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Om du vill göra servern tillgänglig aktiverar du nyckeln på den återställda servern. Välj**omvalidat nyckel för** **datakryptering** > .

   > [!NOTE]
   > Det första försöket att förnya misslyckas, eftersom den nya serverns tjänsthuvudnamn måste få åtkomst till nyckelvalvet. Om du vill generera tjänstens huvudnamn väljer du **Revalidate-nyckel**, som visar ett fel men genererar tjänstens huvudnamn. Därefter hänvisar du till [dessa steg](#set-the-right-permissions-for-key-operations) tidigare i den här artikeln.

   ![Skärmbild av Azure Database för PostgreSQL, med förlängningssteg markerat](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Du måste ge nyckelvalvet åtkomst till den nya servern.

4. När du har registrerat tjänstens huvudnamn, förnya nyckeln igen och servern återupptar sin normala funktionalitet.

   ![Skärmbild av Azure Database for PostgreSQL, med återställd funktionalitet](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Använda en Azure Resource Manager-mall för att aktivera datakryptering

Förutom Azure-portalen kan du också aktivera datakryptering på din Azure-databas för PostgreSQL-en server med Azure Resource Manager-mallar för ny och befintlig server.

### <a name="for-a-new-server"></a>För en ny server

Använd en av de förskapade Azure Resource Manager-mallarna för att etablera servern med datakryptering aktiverad: [Exempel med datakryptering](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Den här Azure Resource Manager-mallen skapar en Azure-databas för PostgreSQL Single-server och använder **KeyVault** och **Key** som skickas som parametrar för att aktivera datakryptering på servern.

### <a name="for-an-existing-server"></a>För en befintlig server
Dessutom kan du använda Azure Resource Manager-mallar för att aktivera datakryptering på din befintliga Azure-databas för PostgreSQL Single-servrar.

* Skicka URI-värden för Azure Key Vault-nyckeln `keyVaultKeyUri` som du kopierade tidigare under egenskapen i egenskapsobjektet.

* Använd *2020-01-01-preview* som API-version.

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 Mer information om datakryptering finns i [Azure Database for PostgreSQL Single server data encryption with customer-managed key](concepts-data-encryption-postgresql.md).
