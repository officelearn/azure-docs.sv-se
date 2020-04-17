---
title: Kryptering i vila med kundhanterade nycklar
description: Lär dig mer om kryptering i resten av ditt Azure-behållarregister och hur du krypterar registret med en kundhanterad nyckel som lagras i Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461760"
---
# <a name="encryption-using-customer-managed-keys"></a>Kryptering med kundhanterade nycklar

När du lagrar avbildningar och andra artefakter i ett Azure-behållarregister krypterar Azure automatiskt registerinnehållet i vila med [tjänsthanterade nycklar](../security/fundamentals/encryption-atrest.md#data-encryption-models). Du kan komplettera standardkryptering med ett extra krypteringslager med en nyckel som du skapar och hanterar i Azure Key Vault. I den här artikeln får du hjälp med stegen med Azure CLI och Azure-portalen.

Kryptering på serversidan med kundhanterade nycklar stöds genom integrering med [Azure Key Vault](../key-vault/general/overview.md). Du kan skapa dina egna krypteringsnycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera krypteringsnycklar. Med Azure Key Vault kan du också granska nyckelanvändning.

Den här funktionen **Premium** är tillgänglig på premium-behållarregistertjänstnivån. Information om registertjänstnivåer och -begränsningar finns i [Azure Container Registry SKU: er](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar](#preview-limitations) gäller. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>
   
## <a name="preview-limitations"></a>Begränsningar för förhandsversion 

* Du kan för närvarande bara aktivera den här funktionen när du skapar ett register.
* När du har aktiverat en kundhanterad nyckel i ett register kan du inte inaktivera den.
* [Innehållsförtroende](container-registry-content-trust.md) stöds för närvarande inte i ett register krypterat med en kundhanterad nyckel.
* I ett register krypterat med en kundhanterad nyckel behålls för närvarande körningsloggar för [ACR-uppgifter](container-registry-tasks-overview.md) i endast 24 timmar. Om du behöver behålla loggar under en längre period läser du vägledning för att [exportera och lagra aktivitetskörningsloggar](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Krav

Om du vill använda Azure CLI-stegen i den här artikeln behöver du Azure CLI version 2.2.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Aktivera kundhanterad nyckel - CLI

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du kommandot [az-grupp skapa][az-group-create] för att skapa en resursgrupp för att skapa nyckelvalvet, behållarregistret och andra nödvändiga resurser.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användartilldelad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) med kommandot [az identity create.][az-identity-create] Den här identiteten används av registret för att komma åt tjänsten Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

I kommandoutdata bör du notera `id` följande `principalId`värden: och . Du behöver dessa värden i senare steg för att konfigurera registeråtkomst till nyckelvalvet.

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

För enkelhetens skull, förvara dessa värden i miljövariabler:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa ett nyckelvalv med [az keyvault skapa][az-keyvault-create] för att lagra en kundhanterad nyckel för registerkryptering. 

Om du vill förhindra dataförlust orsakad av oavsiktliga borttagningar av nyckel- eller nyckelvalv måste du aktivera följande inställningar: **Mjukt borttagnings-** och **rensningsskydd**. Följande exempel innehåller parametrar för dessa inställningar: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Lägg till åtkomstprincip för nyckelvalv

Konfigurera en princip för nyckelvalvet så att identiteten kan komma åt den. I följande [az keyvault-set-policy-kommando][az-keyvault-set-policy] skickar du huvud-ID:t för den hanterade identitet som du skapade och som lagrats tidigare i en miljövariabel. Ange nyckelbehörigheter för att **hämta,** **ta bort nyckeln**och **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Skapa nyckel och hämta nyckel-ID

Kör kommandot [az keyvault-tangenten skapa][az-keyvault-key-create] för att skapa en nyckel i nyckelvalvet.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

I kommandoutdata bör du ta del av `kid`nyckelns ID. Du använder det här ID:et i nästa steg:

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
För enkelhetens skull, förvara det här värdet i en miljövariabel:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Skapa ett register med kundhanterad nyckel

Kör kommandot [az acr create][az-acr-create] för att skapa ett register och aktivera den kundhanterade nyckeln. Skicka det hanterade identitetshuvudet och nyckel-ID:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Visa krypteringsstatus

Om du vill visa om registerkryptering med en kundhanterad nyckel är aktiverad kör du kommandot [az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Aktivera kundhanterad nyckel - portal

### <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Skapa en användartilldelad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i Azure-portalen. Steg om du vill använda en [användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Anteckna **resursnamnet** för den hanterade identiteten. Du behöver det här namnet i senare steg.

![Skapa användartilldelade hanterade identitet i Azure-portalen](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Steg för att skapa ett nyckelvalv finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../key-vault/secrets/quick-create-portal.md).

När du skapar ett nyckelvalv för en kundhanterad nyckel måste du på fliken **Grunderna** aktivera följande skyddsinställningar: **Mjukt borttagnings-** och **rensningsskydd**. De här inställningarna förhindrar dataförlust som orsakas av oavsiktliga borttagningar av nyckel eller nyckelvalv.

![Skapa nyckelvalv i Azure-portalen](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Lägg till åtkomstprincip för nyckelvalv

Konfigurera en princip för nyckelvalvet så att identiteten kan komma åt den.

1. Navigera till ditt nyckelvalv.
1. Välj **principer för inställningar** > **> +Lägg till åtkomstprincip**.
1. Välj **Nyckelbehörigheter**och välj **Hämta,** **Ta bort nyckel**och **radbryt nyckel**.
1. Välj **huvudnamn** och välj resursnamnet för den användartilldelade hanterade identiteten.  
1. Välj **Lägg till**och välj sedan **Spara**.

![Skapa åtkomstprincip för nyckelvalv](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Skapa nyckel

1. Navigera till ditt nyckelvalv.
1. Välj **Inställningstangenter** > **Keys**.
1. Välj **+Generera/importera** och ange ett unikt namn för nyckeln.
1. Acceptera de återstående standardvärdena och välj **Skapa**.
1. När du har skapat det väljer du nyckeln och noterar den aktuella nyckelversionen.

### <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

1. Välj **Skapa ett** > **behållareregister för resursbehållare** > **Container Registry**.
1. Markera eller skapa en resursgrupp på fliken **Grunderna** och ange ett registernamn. Välj **Premium**i **SKU**.
1. Välj Aktiverad i **Kundhanterad** **Enabled**nyckel på fliken **Kryptering** .
1. Välj den hanterade identitet som du skapade i **Identity.**
1. Välj **Välj från Nyckelvalv i** **krypteringsnyckel**.
1. I fönstret **Välj nyckel från Azure Key Vault** väljer du nyckelvalvet, nyckeln och versionen som du skapade i föregående avsnitt.
1. Välj Granska + **skapa**på fliken **Kryptering** .
1. Välj **Skapa** om du vill distribuera registerinstansen.

![Skapa ett containerregister med Azure-portalen](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Om du vill se krypteringsstatus för registret i portalen navigerar du till registret. Under **Inställningar**väljer du **Kryptering (förhandsgranskning)**.

## <a name="enable-customer-managed-key---template"></a>Aktivera kundhanterad nyckel - mall

Du kan också använda en Resource Manager-mall för att skapa ett register och aktivera kryptering med en kundhanterad nyckel. 

I följande mall skapas ett nytt behållarregister och en användartilldelad hanterad identitet. Kopiera följande innehåll till en ny fil och spara `CMKtemplate.json`det med ett filnamn, till exempel .

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

* Nyckelvalv, identifierat med namn
* Nyckelvalvsnyckel, identifierad med nyckel-ID

Kör följande kommando för [att skapa az-gruppdistribution][az-group-deployment-create] för att skapa registret med hjälp av den föregående mallfilen. Ange ett nytt registernamn och ett nytt identitetsnamn samt nyckelvalvets namn och nyckel-ID som du skapade. 

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

### <a name="show-encryption-status"></a>Visa krypteringsstatus

Om du vill visa status för registerkryptering kör du kommandot [az acr encryption show-status][az-acr-encryption-show-status] :

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Använda registret

När du har aktiverat ett register för att kryptera data med en kundhanterad nyckel kan du utföra samma registeråtgärder som du utför i ett register som inte är krypterat med en kundhanterad nyckel. Du kan till exempel autentisera med registret och push Docker-avbildningar. Se exempelkommandon i [Push och hämta en bild](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotera tangent

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt dina efterlevnadsprinciper. Skapa en ny nyckel och uppdatera sedan registret för att kryptera data med den nya nyckeln. Du kan utföra dessa steg med Hjälp av Azure CLI eller i portalen.

Kör till exempel kommandot [az keyvault key create][az-keyvault-key-create] för att skapa en ny nyckel:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Kör sedan kommandot [az acr-kryptering rotate-key,][az-acr-encryption-rotate-key] skicka det nya nyckel-ID:et och huvud-ID:n för den hanterade identitet som du tidigare konfigurerat:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Återkalla nyckel

Återkalla den kundhanterade krypteringsnyckeln genom att ändra åtkomstprincipen i nyckelvalvet eller genom att ta bort nyckeln. Använd till exempel kommandot [az keyvault delete-policy][az-keyvault-delete-policy] för att ändra åtkomstprincipen för den hanterade identitet som används av registret. Ett exempel:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Om du återkallar nyckeln blockeras åtkomsten till alla registerdata effektivt, eftersom registret inte kan komma åt krypteringsnyckeln. Om åtkomsten till nyckeln är aktiverad eller om den borttagna nyckeln återställs, väljer registret nyckeln så att du kan komma åt de krypterade registerdatana igen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md).
* Läs mer om åtkomstprinciper och hur du [skyddar åtkomsten till ett nyckelvalv](../key-vault/general/secure-your-key-vault.md).
* Om du vill ge feedback om kundhanterade nycklar för Azure Container Registry besöker du [ACR GitHub-webbplatsen](https://aka.ms/acr/issues).


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
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
