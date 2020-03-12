---
title: Kryptering – rest med Kundhanterade nycklar
description: Lär dig mer om kryptering – resten av Azure Container Registry och hur du krypterar registret med en kundhanterad nyckel som lagras i Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 8bce77c776fe088e5c317f02cd2757738a287069
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096481"
---
# <a name="encryption-using-customer-managed-keys"></a>Kryptering med Kundhanterade nycklar

När du lagrar bilder och andra artefakter i ett Azure Container Registry krypterar Azure automatiskt register innehållet i vila med [tjänst hanterade nycklar](../security/fundamentals/encryption-atrest.md#data-encryption-models). Du kan komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av en nyckel som du skapar och hanterar i Azure Key Vault. Den här artikeln vägleder dig genom stegen i Azure CLI och Azure Portal.

Kryptering på Server sidan med Kundhanterade nycklar stöds genom integrering med [Azure Key Vault](../key-vault/key-vault-overview.md). Du kan skapa egna krypterings nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault s API: er för att generera krypterings nycklar. Med Azure Key Vault kan du också granska nyckel användningen.

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry SKU: er](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar](#preview-limitations) gäller. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>
   
## <a name="preview-limitations"></a>Begränsningar för förhandsversion 

* Du kan för närvarande endast aktivera den här funktionen när du skapar ett register.
* När du har aktiverat en kundhanterad nyckel i ett register kan du inte inaktivera den.
* I ett register som är krypterat med en kundhanterad nyckel behålls kör loggar för [ACR-aktiviteter](container-registry-tasks-overview.md) för närvarande endast i 24 timmar. Om du behöver behålla loggarna under en längre period, se rikt linjer för att [Exportera och lagra aktivitets körnings loggar](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-stegen i den här artikeln måste du ha Azure CLI version 2.2.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Aktivera kundhanterad nyckel-CLI

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp för att skapa nyckel valvet, container Registry och andra nödvändiga resurser.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användardefinierad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) med kommandot [AZ Identity Create][az-identity-create] . Den här identiteten används av ditt register för att få åtkomst till tjänsten Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

I kommandot utdata noterar du följande värden: `id` och `principalId`. Du behöver dessa värden i senare steg för att konfigurera register åtkomst till nyckel valvet.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

För enkelhetens skull kan du lagra dessa värden i miljövariabler:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa ett nyckel valv med [AZ-valv skapa][az-keyvault-create] för att lagra en kundhanterad nyckel för register kryptering. 

För att förhindra data förlust som orsakas av oavsiktlig nyckel eller nyckel valv borttagningar måste du aktivera följande inställningar: **mjuk borttagning** och **tömning av skydd**. Följande exempel innehåller parametrar för de här inställningarna: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Lägg till åtkomst princip för nyckel valv

Konfigurera en princip för nyckel valvet så att identiteten kan komma åt den. I följande AZ för nyckel [valv anger][az-keyvault-set-policy] du huvud-ID: t för den hanterade identitet som du har skapat, lagrad tidigare i en miljö variabel. Ange nyckel behörigheter för att **Hämta**, **unwrapKey**och **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Skapa nyckel och hämta nyckel-ID

Kör kommandot [AZ Key Vault Key Create][az-keyvault-key-create] för att skapa en nyckel i nyckel valvet.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

I kommandot utdata noterar du nyckelns ID, `kid`. Du använder det här ID: t i nästa steg:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
För enkelhetens skull kan du lagra det här värdet i en miljö variabel:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Skapa ett register med kundhanterad nyckel

Kör kommandot [AZ ACR Create][az-acr-create] för att skapa ett register och aktivera den Kundhanterade nyckeln. Skicka det hanterade identitetens huvud-ID och nyckel-ID: t som tidigare fanns i miljövariabler:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Visa krypterings status

Om du vill visa om register kryptering med en kundhanterad nyckel är aktive rad kör du kommandot [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Aktivera kundhanterad nyckel – Portal

### <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Skapa en användardefinierad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i Azure Portal. Anvisningar finns i [skapa en användardefinierad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Anteckna **resurs namnet** för den hanterade identiteten. Du behöver det här namnet i senare steg.

![Skapa användardefinierad hanterad identitet i Azure Portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Anvisningar för hur du skapar ett nyckel valv finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../key-vault/quick-create-portal.md).

När du skapar ett nyckel valv för en kundhanterad nyckel måste du aktivera följande skydds inställningar på fliken **grundläggande** : **mjuk borttagning** och tömning av **skydd**. De här inställningarna hjälper till att förhindra data förlust som orsakas av oavsiktlig nyckel eller nyckel valv borttagningar.

![Skapa nyckel valv i Azure Portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Lägg till åtkomst princip för nyckel valv

Konfigurera en princip för nyckel valvet så att identiteten kan komma åt den.

1. Navigera till ditt nyckel valv.
1. Välj **inställningar** > **åtkomst principer > + Lägg till åtkomst princip**.
1. Välj **nyckel behörigheter**och välj **Hämta**, **unwrap Key**och **wrap Key**.
1. Välj **Välj huvud** namn och Välj resurs namnet för den hanterade identiteten som tilldelats av användaren.  
1. Välj **Lägg till**och välj sedan **Spara**.

![Skapa åtkomst princip för nyckel valv](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Skapa nyckel

1. Navigera till ditt nyckel valv.
1. Välj **inställningar** > **nycklar**.
1. Välj **+ generera/importera** och ange ett unikt namn för nyckeln.
1. Acceptera återstående standardvärden och välj **skapa**.
1. När du har skapat väljer du nyckeln och noterar den aktuella nyckel versionen.

### <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

1. Välj **Skapa en resurs** > **Containers** > **Container Registry**.
1. På fliken **grundläggande** , Välj eller skapa en resurs grupp och ange ett register namn. I **SKU**väljer du **Premium**.
1. På fliken **kryptering** i **kundhanterad nyckel**väljer du **aktive rad**.
1. I **identitet**väljer du den hanterade identitet som du skapade.
1. I **krypterings nyckel**väljer du **Välj från Key Vault**.
1. I fönstret **Välj nyckel från Azure Key Vault** väljer du nyckel valvet, nyckeln och versionen som du skapade i föregående avsnitt.
1. På fliken **kryptering** väljer du **Granska + skapa**.
1. Välj **skapa** för att distribuera register instansen.

![Skapa ett containerregister med Azure-portalen](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Om du vill se krypterings status för registret i portalen går du till registret. Under **Inställningar**väljer du **kryptering (för hands version)** .

## <a name="enable-customer-managed-key---template"></a>Aktivera kundhanterad nyckel-mall

Du kan också använda en Resource Manager-mall för att skapa ett register och aktivera kryptering med en kundhanterad nyckel. 

Följande mall skapar ett nytt behållar register och en användardefinierad hanterad identitet. Kopiera följande innehåll till en ny fil och spara det med ett fil namn, till exempel `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Följ stegen i föregående avsnitt för att skapa följande resurser:

* Nyckel valv, identifierat med namn
* Nyckel valv nyckel, identifierad efter nyckel-ID

Kör följande kommando för [AZ Group Deployment Create][az-group-deployment-create] för att skapa registret med hjälp av föregående mallfil. Ange ett nytt register namn och namn på den hanterade identiteten, samt namnet på nyckel valvet och nyckel-ID: t som du skapade. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Visa krypterings status

Om du vill visa status för register kryptering kör du kommandot [AZ ACR Encryption show-status] [AZ-ACR-Encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Använda registret

När du har aktiverat ett register för att kryptera data med en kundhanterad nyckel kan du utföra samma register åtgärder som du utför i ett register som inte är krypterat med en kundhanterad nyckel. Du kan till exempel autentisera med registret och push Docker-avbildningar. Se exempel kommandon i [push och hämta en avbildning](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotations nyckel

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. Skapa en ny nyckel och uppdatera sedan registret för att kryptera data med hjälp av den nya nyckeln. Du kan utföra dessa steg med hjälp av Azure CLI eller i portalen.

Du kan till exempel köra kommandot [AZ Key Vault Create][az-keyvault-key-create] för att skapa en ny nyckel:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Kör sedan kommandot [AZ ACR Encryption rotation-Key][az-acr-encryption-rotate-key] och skicka det nya nyckel-ID: t och ägar-ID: t för den hanterade identitet som du tidigare har konfigurerat:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Återkalla nyckel

Återkalla den Kundhanterade krypterings nyckeln genom att ändra åtkomst principen i nyckel valvet eller genom att ta bort nyckeln. Använd exempelvis kommandot [AZ-][az-keyvault-delete-policy] för att ändra åtkomst principen för den hanterade identitet som används av registret. Exempel:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Att återkalla nyckeln på ett effektivt sätt blockerar åtkomsten till alla register data, eftersom registret inte har åtkomst till krypterings nyckeln. Om åtkomst till nyckeln är aktive rad eller om den borttagna nyckeln återställs, kommer ditt register att välja nyckeln så att du kan komma åt krypterade register data igen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md).
* Lär dig mer om åtkomst principer och hur du [skyddar åtkomsten till ett nyckel valv](../key-vault/key-vault-secure-your-key-vault.md).
* Om du vill ge feedback om Kundhanterade nycklar för Azure Container Registry går du till [webbplatsen för ACR GitHub](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/keyt#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault/keyt#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault/keyt#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
