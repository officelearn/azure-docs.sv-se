---
title: Aktivera hanterad identitet i behållar gruppen
description: Lär dig hur du aktiverar en hanterad identitet i Azure Container Instances som kan autentiseras med andra Azure-tjänster
ms.topic: article
ms.date: 10/22/2018
ms.openlocfilehash: b5546e8c4b512b584a57e8e4c2ff46c52ab856a0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533682"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Använda hanterade identiteter med Azure Container Instances

Använd [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) för att köra kod i Azure Container instances som samverkar med andra Azure-tjänster – utan att behålla några hemligheter eller autentiseringsuppgifter i kod. Funktionen tillhandahåller en Azure Container Instances-distribution med en automatiskt hanterad identitet i Azure Active Directory.

I den här artikeln får du lära dig mer om hanterade identiteter i Azure Container Instances och:

> [!div class="checklist"]
> * Aktivera en användardefinierad eller tilldelad identitet i en behållar grupp
> * Ge identitets åtkomst till en Azure Key Vault
> * Använd den hanterade identiteten för att få åtkomst till en Key Vault från en behållare som körs

Anpassa exemplen för att aktivera och använda identiteter i Azure Container Instances för att få åtkomst till andra Azure-tjänster. Dessa exempel är interaktiva. Men i praktiken skulle behållar avbildningarna köra kod för att få åtkomst till Azure-tjänster.

> [!NOTE]
> För närvarande kan du inte använda en hanterad identitet i en behållar grupp som distribuerats till ett virtuellt nätverk.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

Använd en hanterad identitet i en behållare som körs för att autentisera till en [tjänst som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) utan att hantera autentiseringsuppgifter i behållar koden. För tjänster som inte stöder AD-autentisering kan du lagra hemligheter i Azure Key Vault och använda den hanterade identiteten för att komma åt Key Vault för att hämta autentiseringsuppgifter. Mer information om hur du använder en hanterad identitet finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA). För närvarande stöds endast hanterade identiteter på Linux container instances.
>  

### <a name="enable-a-managed-identity"></a>Aktivera en hanterad identitet

 I Azure Container Instances stöds hanterade identiteter för Azure-resurser från REST API version 2018-10-01 och motsvarande SDK: er och verktyg. När du skapar en behållar grupp aktiverar du en eller flera hanterade identiteter genom att ange en [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) -egenskap. Du kan också aktivera eller uppdatera hanterade identiteter när en behållar grupp körs. båda åtgärderna gör att behållar gruppen startas om. Om du vill ange identiteter för en ny eller befintlig behållar grupp använder du Azure CLI, en Resource Manager-mall eller en YAML-fil. 

Azure Container Instances stöder båda typerna av hanterade Azure-identiteter: användar tilldelning och systemtilldelade. I en behållar grupp kan du aktivera en tilldelad identitet, en eller flera användare som tilldelats identiteter eller båda typerna av identiteter. 

* En **användare som tilldelats** en hanterad identitet skapas som en fristående Azure-resurs i Azure AD-klienten som är betrodd av den prenumeration som används. När identiteten har skapats kan identiteten tilldelas till en eller flera Azure-resurser (i Azure Container Instances eller andra Azure-tjänster). Livs cykeln för en användardefinierad identitet hanteras separat från livs cykeln för de behållar grupper eller andra tjänst resurser som den är tilldelad till. Det här beteendet är särskilt användbart i Azure Container Instances. Eftersom identiteten sträcker sig utanför livs längden för en behållar grupp kan du återanvända den tillsammans med andra standardinställningar för att göra distributionerna i behållar gruppen hög repeterbara.

* En **systemtilldelad** hanterad identitet aktive ras direkt i en behållar grupp i Azure Container instances. När den är aktive rad skapar Azure en identitet för gruppen i Azure AD-klienten som är betrodd av instansens prenumeration. När identiteten har skapats är autentiseringsuppgifterna etablerade i varje behållare i behållar gruppen. Livs cykeln för en tilldelad identitet är direkt knuten till den behållar grupp som den är aktive rad för. När gruppen tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.

### <a name="use-a-managed-identity"></a>Använda en hanterad identitet

Om du vill använda en hanterad identitet måste identiteten först beviljas åtkomst till en eller flera Azure-tjänst resurser (till exempel en webbapp, ett Key Vault eller ett lagrings konto) i prenumerationen. För att få åtkomst till Azure-resurser från en behållare som körs måste din kod hämta en *åtkomsttoken* från en Azure AD-slutpunkt. Koden skickar sedan åtkomsttoken på ett anrop till en tjänst som stöder Azure AD-autentisering. 

Att använda en hanterad identitet i en pågående behållare är i stort sett detsamma som att använda en identitet i en virtuell Azure-dator. Se vägledningen för virtuella datorer för att använda en [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL eller Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)eller [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md): er.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

I exemplen i den här artikeln används en hanterad identitet i Azure Container Instances för att få åtkomst till en Azure Key Vault hemlighet. 

Skapa först en resurs grupp med namnet *myResourceGroup* på den *östra* platsen med följande [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) -kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [AZ-valv](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) för att skapa en Key Vault. Se till att ange ett unikt Key Vault namn. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Lagra en exempel hemlighet i Key Vault med hjälp av kommandot [AZ-valvets hemliga uppsättning](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Fortsätt med följande exempel för att få åtkomst till Key Vault med antingen en tilldelad eller systemtilldelad hanterad identitet i Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exempel 1: Använd en användardefinierad identitet för att få åtkomst till Azure Key Vault

### <a name="create-an-identity"></a>Skapa en identitet

Skapa först en identitet i din prenumeration med kommandot [AZ Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) . Du kan använda samma resurs grupp som användes för att skapa Key Vault, eller använda en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Om du vill använda identiteten i följande steg använder du kommandot [AZ Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) för att lagra identitetens huvud namn-ID och resurs-ID i variabler.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Aktivera en användardefinierad identitet i en behållar grupp

Kör följande [AZ container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) -kommando för att skapa en behållar instans baserat på Ubuntu-servern. I det här exemplet finns en grupp med en behållare som du kan använda för att interaktivt få åtkomst till andra Azure-tjänster. Parametern `--assign-identity` skickar den användar tilldelnings hanterade identiteten till gruppen. Kommandot med lång körning håller behållaren igång. I det här exemplet används samma resurs grupp som användes för att skapa Key Vault, men du kan ange en annan.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrol lera statusen med kommandot [AZ container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Avsnittet `identity` i utdata ser ut ungefär så här, och visar identiteten som anges i behållar gruppen. `principalID` under `userAssignedIdentities` är tjänstens huvud namn för den identitet som du skapade i Azure Active Directory:

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Bevilja användare tilldelad identitets åtkomst till Key Vault

Kör följande AZ-kommando för att [Ange princip](/cli/azure/keyvault?view=azure-cli-latest) för att ange en åtkomst princip för Key Vault. I följande exempel kan den användare som tilldelats identiteten Hämta hemligheter från Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Använd användardefinierad identitet för att hämta hemlighet från Key Vault

Nu kan du använda den hanterade identiteten för att få åtkomst till Key Vault i den pågående behållar instansen. I det här exemplet ska du först starta ett bash-gränssnitt i behållaren:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i behållaren. Kör följande kommando för att få en åtkomsttoken att använda Azure Active Directory för att autentisera till Key Vault:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Resultat:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Om du vill lagra åtkomsttoken i en variabel som ska användas i efterföljande kommandon för att autentisera kör du följande kommando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Använd nu åtkomsttoken för att autentisera till Key Vault och läsa en hemlighet. Se till att du byter namn på nyckel valvet i URL: en ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret ser ut ungefär så här, vilket visar hemligheten. I din kod kan du parsa dessa utdata för att hämta hemligheten. Använd sedan hemligheten i en efterföljande åtgärd för att få åtkomst till en annan Azure-resurs.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exempel 2: Använd en tilldelad identitet för att få åtkomst till Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Aktivera en systemtilldelad identitet i en behållar grupp

Kör följande [AZ container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) -kommando för att skapa en behållar instans baserat på Ubuntu-servern. I det här exemplet finns en grupp med en behållare som du kan använda för att interaktivt få åtkomst till andra Azure-tjänster. Parametern `--assign-identity` utan ytterligare värde aktiverar en systemtilldelad hanterad identitet i gruppen. Kommandot med lång körning håller behållaren igång. I det här exemplet används samma resurs grupp som användes för att skapa Key Vault, men du kan ange en annan.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrol lera statusen med kommandot [AZ container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Avsnittet `identity` i resultatet ser ut ungefär så här, vilket visar att en systemtilldelad identitet skapas i Azure Active Directory:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Ange en variabel till värdet för `principalId` (tjänstens huvud namns-ID) för identiteten som ska användas i senare steg.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Bevilja åtkomst till behållar grupp till Key Vault

Kör följande AZ-kommando för att [Ange princip](/cli/azure/keyvault?view=azure-cli-latest) för att ange en åtkomst princip för Key Vault. I följande exempel kan systemhanterade identiteter Hämta hemligheter från Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Använd container grupp identitet för att hämta hemlighet från Key Vault

Nu kan du använda den hanterade identiteten för att få åtkomst till Key Vault i den pågående behållar instansen. I det här exemplet ska du först starta ett bash-gränssnitt i behållaren:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i behållaren. Kör följande kommando för att få en åtkomsttoken att använda Azure Active Directory för att autentisera till Key Vault:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Resultat:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Om du vill lagra åtkomsttoken i en variabel som ska användas i efterföljande kommandon för att autentisera kör du följande kommando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Använd nu åtkomsttoken för att autentisera till Key Vault och läsa en hemlighet. Se till att du byter namn på nyckel valvet i URL: en (*https:\//mykeyvault.Vault.Azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret ser ut ungefär så här, vilket visar hemligheten. I din kod kan du parsa dessa utdata för att hämta hemligheten. Använd sedan hemligheten i en efterföljande åtgärd för att få åtkomst till en annan Azure-resurs.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Aktivera hanterad identitet med Resource Manager-mall

Om du vill aktivera en hanterad identitet i en behållar grupp med hjälp av en [Resource Manager-mall](container-instances-multi-container-group.md)anger du egenskapen `identity` för `Microsoft.ContainerInstance/containerGroups`-objektet med ett `ContainerGroupIdentity`-objekt. Följande kodfragment visar `identity`-egenskapen som kon figurer ATS för olika scenarier. Se [referens för Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups). Ange en `apiVersion` av `2018-10-01`.

### <a name="user-assigned-identity"></a>Användare tilldelad identitet

En användardefinierad identitet är ett resurs-ID för formuläret:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Du kan aktivera en eller flera användare tilldelade identiteter.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Systemtilldelad identitet

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>System-och användarspecifika identiteter

Du kan aktivera både en tilldelad identitet och en eller flera användarspecifika identiteter i en behållar grupp.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Aktivera hanterad identitet med YAML-fil

Om du vill aktivera en hanterad identitet i en behållar grupp som distribueras med en [yaml-fil](container-instances-multi-container-yaml.md), inkluderar du följande yaml.
Ange en `apiVersion` av `2018-10-01`.

### <a name="user-assigned-identity"></a>Användare tilldelad identitet

En användardefinierad identitet är ett resurs-ID för formuläret 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Du kan aktivera en eller flera användare tilldelade identiteter.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Systemtilldelad identitet

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>System-och användarspecifika identiteter

Du kan aktivera både en tilldelad identitet och en eller flera användarspecifika identiteter i en behållar grupp.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hanterade identiteter i Azure Container Instances och hur du:

> [!div class="checklist"]
> * Aktivera en användardefinierad eller tilldelad identitet i en behållar grupp
> * Ge identitets åtkomst till en Azure Key Vault
> * Använd den hanterade identiteten för att få åtkomst till en Key Vault från en behållare som körs

* Lär dig mer om [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/).

* Se ett [Azure go SDK-exempel](https://medium.com/@samkreter/c98911206328) för att använda en hanterad identitet för att få åtkomst till en Key Vault från Azure Container instances.
