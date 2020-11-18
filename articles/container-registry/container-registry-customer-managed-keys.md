---
title: Kryptera registret med en kundhanterad nyckel
description: Lär dig mer om kryptering – resten av ditt Azure Container Registry och hur du krypterar ditt Premium-register med en kundhanterad nyckel som lagras i Azure Key Vault
ms.topic: article
ms.date: 09/30/2020
ms.custom: ''
ms.openlocfilehash: ad81a94910cb1ed09634801f8706182e17947225
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842574"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Kryptera registret med en kundhanterad nyckel

När du lagrar bilder och andra artefakter i ett Azure Container Registry krypterar Azure automatiskt register innehållet i vila med [tjänst hanterade nycklar](../security/fundamentals/encryption-models.md). Du kan komplettera standard kryptering med ytterligare ett krypterings lager med hjälp av en nyckel som du skapar och hanterar i Azure Key Vault (en kundhanterad nyckel). Den här artikeln vägleder dig genom stegen i Azure CLI och Azure Portal.

Kryptering på Server sidan med Kundhanterade nycklar stöds genom integrering med [Azure Key Vault](../key-vault/general/overview.md). Du kan skapa egna krypterings nycklar och lagra dem i ett nyckel valv eller använda Azure Key Vault s API: er för att generera nycklar. Med Azure Key Vault kan du också granska nyckel användningen.

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).


## <a name="things-to-know"></a>Saker att känna till

* Du kan för närvarande endast aktivera en kundhanterad nyckel när du skapar ett register. När du aktiverar nyckeln konfigurerar du en *användardefinierad* hanterad identitet för att få åtkomst till nyckel valvet.
* När du har aktiverat kryptering med en kundhanterad nyckel i ett register kan du inte inaktivera krypteringen.  
* [Innehålls förtroende](container-registry-content-trust.md) stöds för närvarande inte i ett register som är krypterat med en kundhanterad nyckel.
* I ett register som är krypterat med en kundhanterad nyckel behålls kör loggar för [ACR-aktiviteter](container-registry-tasks-overview.md) för närvarande endast i 24 timmar. Om du behöver behålla loggarna under en längre period, se rikt linjer för att [Exportera och lagra aktivitets körnings loggar](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Om åtkomst till ditt Azure Key Vault är begränsat med ett virtuellt nätverk med en [Key Vault brand vägg](../key-vault/general/network-security.md)krävs ytterligare konfigurations steg. När du har skapat registret och aktiverat den Kundhanterade nyckeln, ställer du in åtkomst till nyckeln med hjälp av registrets *tilldelade* hanterade identitet och konfigurerar registret för att kringgå Key Vault brand väggen. Följ anvisningarna i den här artikeln först för att aktivera kryptering med en kundhanterad nyckel och se rikt linjerna för [avancerade scenarier: Key Vault brand vägg](#advanced-scenario-key-vault-firewall) senare i den här artikeln.

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

I kommandot utdata noterar du följande värden: `id` och `principalId` . Du behöver dessa värden i senare steg för att konfigurera register åtkomst till nyckel valvet.

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

För att förhindra data förlust som orsakas av oavsiktlig nyckel eller nyckel valvs borttagningar aktiverar du följande inställningar: **mjuk borttagning** och **tömning av skydd**. Följande exempel innehåller parametrar för de här inställningarna:

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

> [!NOTE]
> Från och med Azure CLI version 2,2 `az keyvault create` aktiverar mjuk borttagning som standard.

Hämta resurs-ID för nyckel valvet för användning i senare steg:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Aktivera åtkomst till nyckel valv

Konfigurera en princip för nyckel valvet så att identiteten kan komma åt den. I följande AZ för nyckel [valv anger][az-keyvault-set-policy] du huvud-ID: t för den hanterade identitet som du har skapat, lagrad tidigare i en miljö variabel. Ange nyckel behörigheter för att **Hämta**, **unwrapKey** och **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Du kan också använda [Azure RBAC för Key Vault](../key-vault/general/rbac-guide.md) (för hands version) för att tilldela behörigheter till identiteten för att få åtkomst till nyckel valvet. Tilldela till exempel krypterings rollen Key Vault krypterings tjänst till identiteten med hjälp av kommandot [AZ roll tilldelning Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Skapa nyckel och hämta nyckel-ID

Kör kommandot [AZ Key Vault Key Create][az-keyvault-key-create] för att skapa en nyckel i nyckel valvet.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Anteckna nyckelns ID i kommandot utdata `kid` . Du använder det här ID: t i nästa steg:

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
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Skapa ett register med kundhanterad nyckel

Kör kommandot [AZ ACR Create][az-acr-create] för att skapa ett register i Premium service-nivån och aktivera kundhanterad nyckel. Skicka det hanterade identitetens huvud-ID och nyckel-ID: t som tidigare fanns i miljövariabler:

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

Utdata liknar följande:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Aktivera kundhanterad nyckel – Portal

### <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Skapa en användardefinierad [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i Azure Portal. Anvisningar finns i [skapa en användardefinierad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Du kan använda identitetens namn i senare steg.

![Skapa användardefinierad hanterad identitet i Azure Portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Anvisningar för hur du skapar ett nyckel valv finns i [snabb start: skapa en Azure Key Vault med Azure Portal](../key-vault/general/quick-create-portal.md).

När du skapar ett nyckel valv för en kundhanterad nyckel, på fliken **grundläggande** , aktiverar du följande skydds inställningar: **mjuk borttagning** och **tömning av skydd**. De här inställningarna hjälper till att förhindra data förlust som orsakas av oavsiktlig nyckel eller nyckel valv borttagningar.

![Skapa nyckel valv i Azure Portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="enable-key-vault-access"></a>Aktivera åtkomst till nyckel valv

Konfigurera en princip för nyckel valvet så att identiteten kan komma åt den.

1. Navigera till ditt nyckel valv.
1. Välj **Inställningar**  >  **åtkomst principer > + Lägg till åtkomst princip**.
1. Välj **nyckel behörigheter** och välj **Hämta**, **unwrap Key** och **wrap Key**.
1. Välj **Välj huvud** namn och Välj resurs namnet för den hanterade identiteten som tilldelats av användaren.  
1. Välj **Lägg till** och välj sedan **Spara**.

![Skapa åtkomst princip för nyckel valv](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

 Du kan också använda [Azure RBAC för Key Vault](../key-vault/general/rbac-guide.md) (för hands version) för att tilldela behörigheter till identiteten för att få åtkomst till nyckel valvet. Tilldela till exempel krypterings rollen Key Vault krypterings tjänst till identiteten.

1. Navigera till ditt nyckel valv.
1. Välj **åtkomst kontroll (IAM)**  >  **+ Lägg** till  >  **Lägg till roll tilldelning**.
1. I fönstret **Lägg till roll tilldelning** :
    1. Välj Key Vault rollen kryptering av krypterings **tjänst (för hands version)** . 
    1. Tilldela åtkomst till **användare som tilldelats en hanterad identitet**.
    1. Välj resurs namnet för din användarspecifika hanterade identitet och välj **Spara**.

### <a name="create-key"></a>Skapa nyckel

1. Navigera till ditt nyckel valv.
1. Välj **Inställningar**  >  **nycklar**.
1. Välj **+ generera/importera** och ange ett unikt namn för nyckeln.
1. Acceptera återstående standardvärden och välj **skapa**.
1. När du har skapat väljer du nyckeln och noterar den aktuella nyckel versionen.

### <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

1. Välj **skapa en resurs**  >  **behållare**  >  **container Registry**.
1. På fliken **grundläggande** , Välj eller skapa en resurs grupp och ange ett register namn. I **SKU** väljer du **Premium**.
1. På fliken **kryptering** i **kundhanterad nyckel** väljer du **aktive rad**.
1. I **identitet** väljer du den hanterade identitet som du skapade.
1. I **kryptering** väljer du **Välj från Key Vault**.
1. I fönstret **Välj nyckel från Azure Key Vault** väljer du nyckel valvet, nyckeln och versionen som du skapade i föregående avsnitt.
1. På fliken **kryptering** väljer du **Granska + skapa**.
1. Välj **skapa** för att distribuera register instansen.

![Skapa ett containerregister med Azure-portalen](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Om du vill se krypterings status för registret i portalen går du till registret. Under **Inställningar** väljer du  **kryptering**.

## <a name="enable-customer-managed-key---template"></a>Aktivera kundhanterad nyckel-mall

Du kan också använda en Resource Manager-mall för att skapa ett register och aktivera kryptering med en kundhanterad nyckel.

Följande mall skapar ett nytt behållar register och en användardefinierad hanterad identitet. Kopiera följande innehåll till en ny fil och spara det med ett fil namn, till exempel `CMKtemplate.json` .

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

Om du vill visa status för register kryptering kör du kommandot [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Använda registret

När du har aktiverat en kundhanterad nyckel i ett register kan du utföra samma register åtgärder som du utför i ett register som inte är krypterat med en kundhanterad nyckel. Du kan till exempel autentisera med registret och push Docker-avbildningar. Se exempel kommandon i [push och hämta en avbildning](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotations nyckel

Rotera en kundhanterad nyckel som används för register kryptering enligt dina efterlevnadsprinciper. Skapa en ny nyckel eller uppdatera en nyckel version och uppdatera sedan registret för att kryptera data med hjälp av nyckeln. Du kan utföra dessa steg med hjälp av Azure CLI eller i portalen.

När du roterar en nyckel anger du vanligt vis samma identitet som när du skapar registret. Du kan också konfigurera en ny användardefinierad identitet för nyckel åtkomst eller aktivera och ange registrets systemtilldelade identitet.

> [!NOTE]
> Se till att den identitet som krävs för åtkomst till nyckel [valvet](#enable-key-vault-access) har angetts för den identitet du konfigurerar.

### <a name="azure-cli"></a>Azure CLI

Använd [AZ Key Vault Key][az-keyvault-key] commands för att skapa eller hantera nyckel valv nycklar. Om du till exempel vill skapa en ny nyckel version eller nyckel kör du kommandot [AZ Key Vault Key Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name>
```

Kör sedan kommandot [AZ ACR Encryption rotation-Key][az-acr-encryption-rotate-key] och skicka det nya nyckel-ID: t och identiteten som du vill konfigurera:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>Portalen

Använd registrets **krypterings** inställningar för att uppdatera nyckel versionen, nyckeln, nyckel valvet eller identitets inställningarna som används för den Kundhanterade nyckeln.

Till exempel för att skapa och konfigurera en ny nyckel version:

1. I portalen navigerar du till ditt register.
1. Under **Inställningar** väljer du **krypterings**  >  **ändrings nyckel**.
1. Välj **Välj nyckel**

    ![Rotera nyckel i Azure Portal](./media/container-registry-customer-managed-keys/rotate-key.png)
1. I fönstret **Välj nyckel från Azure Key Vault** väljer du nyckel valvet och nyckeln som du konfigurerade tidigare och i **version** väljer du **Skapa ny**.
1. I fönstret **skapa en nyckel** väljer du **generera** och sedan **skapa**.
1. Slutför valet av nyckel och välj **Spara**.

## <a name="revoke-key"></a>Återkalla nyckel

Återkalla den Kundhanterade krypterings nyckeln genom att ändra åtkomst principen eller behörigheterna för nyckel valvet eller genom att ta bort nyckeln. Använd exempelvis kommandot [AZ-][az-keyvault-delete-policy] för att ändra åtkomst principen för den hanterade identitet som används i registret:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Att återkalla nyckeln på ett effektivt sätt blockerar åtkomsten till alla register data, eftersom registret inte har åtkomst till krypterings nyckeln. Om åtkomst till nyckeln är aktive rad eller om den borttagna nyckeln återställs, kommer ditt register att välja nyckeln så att du kan komma åt krypterade register data igen.

## <a name="advanced-scenario-key-vault-firewall"></a>Avancerat scenario: Key Vault brand vägg

Om ditt Azure Key Vault har distribuerats i ett virtuellt nätverk med en Key Vault brand vägg utför du följande ytterligare steg när du har aktiverat kundhanterad nyckel kryptering i registret.

1. Konfigurera register kryptering för att använda registrets tilldelade identitet
1. Aktivera registret för att kringgå Key Vault brand väggen
1. Rotera den Kundhanterade nyckeln

### <a name="configure-system-assigned-identity"></a>Konfigurera systemtilldelad identitet

Du kan konfigurera ett registers systemtilldelad hanterad identitet för att få åtkomst till nyckel valvet för krypterings nycklar. Om du inte känner till de olika hanterade identiteterna för Azure-resurser kan du läsa mer i [översikten](../active-directory/managed-identities-azure-resources/overview.md).

Så här aktiverar du registrets systemtilldelade identitet i portalen:

1. I portalen navigerar du till ditt register.
1. Välj **Inställningar**  >   **identitet**.
1. Under **systemtilldelat**, ställer du in **status** **på på**. Välj **Spara**.
1. Kopiera identitetens **objekt-ID** .

Så här ger du identitets åtkomst till ditt nyckel valv:

1. Navigera till ditt nyckel valv.
1. Välj **Inställningar**  >  **åtkomst principer > + Lägg till åtkomst princip**.
1. Välj **nyckel behörigheter** och välj **Hämta**, **unwrap Key** och **wrap Key**.
1. Välj **Välj huvud namn** och Sök efter objekt-ID: t för den systemtilldelade hanterade identiteten eller namnet på registret.  
1. Välj **Lägg till** och välj sedan **Spara**.

Så här uppdaterar du registrets krypterings inställningar för att använda identiteten:

1. I portalen navigerar du till ditt register.
1. Under **Inställningar** väljer du **krypterings**  >  **ändrings nyckel**.
1. I **identitet** väljer du **tilldelat system** och väljer **Spara**.

### <a name="enable-key-vault-bypass"></a>Aktivera kringgå nyckel valv

För att få åtkomst till ett nyckel valv som kon figurer ATS med en Key Vault brand vägg, måste registret kringgå brand väggen. Kontrol lera att nyckel valvet har kon figurer ATS för att tillåta åtkomst av [betrodda tjänster](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry är en av de betrodda tjänsterna.

1. I portalen navigerar du till ditt nyckel valv.
1. Välj **Inställningar**  >  **nätverk**.
1. Bekräfta, uppdatera eller Lägg till inställningar för virtuellt nätverk. Detaljerade anvisningar finns i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](../key-vault/general/network-security.md).
1. I **Tillåt Microsoft-betrodda tjänster att kringgå den här brand väggen väljer du** **Ja**. 

### <a name="rotate-the-customer-managed-key"></a>Rotera den Kundhanterade nyckeln

När du har slutfört föregående steg roterar du nyckeln till en ny nyckel i nyckel valvet bakom en brand vägg. Anvisningar finns i [rotations nyckel](#rotate-key) i den här artikeln.

## <a name="troubleshoot"></a>Felsöka

### <a name="removing-user-assigned-identity"></a>Tar bort användardefinierad identitet

Om du försöker ta bort en tilldelad identitet från ett register som används för kryptering kan du se ett fel meddelande som liknar:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Du kan inte heller ändra krypterings nyckeln (rotera). Om det här problemet uppstår måste du först tilldela om identiteten med hjälp av det GUID som visas i fel meddelandet. Exempel:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
När du har ändrat nyckeln och tilldelat en annan identitet kan du ta bort den ursprungliga användarens identitet.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md).
* Lär dig mer om åtkomst principer och hur du [skyddar åtkomsten till ett nyckel valv](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
