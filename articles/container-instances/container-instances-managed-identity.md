---
title: Aktivera hanterad identitet i behållargruppen
description: Lär dig hur du aktiverar en hanterad identitet i Azure Container Instances som kan autentisera med andra Azure-tjänster
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 19d2ab22eea15278c7753046f9222c7856fbf5ef
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685646"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Så här använder du hanterade identiteter med Azure Container Instances

Använd [hanterade identiteter för Azure-resurser för](../active-directory/managed-identities-azure-resources/overview.md) att köra kod i Azure Container Instances som interagerar med andra Azure-tjänster – utan att underhålla några hemligheter eller autentiseringsuppgifter i koden. Funktionen tillhandahåller en Azure Container Instances-distribution med en automatiskt hanterad identitet i Azure Active Directory.

I den här artikeln lär du dig mer om hanterade identiteter i Azure Container Instances och:

> [!div class="checklist"]
> * Aktivera en användartilldelad eller systemtilldelad identitet i en behållargrupp
> * Bevilja identitetsåtkomst till ett Azure-nyckelvalv
> * Använd den hanterade identiteten för att komma åt ett nyckelvalv från en behållare som körs

Anpassa exemplen för att aktivera och använda identiteter i Azure Container Instances för att komma åt andra Azure-tjänster. Dessa exempel är interaktiva. I praktiken skulle dock behållaravbildningar köra kod för att komma åt Azure-tjänster.

> [!NOTE]
> För närvarande kan du inte använda en hanterad identitet i en behållargrupp som distribueras till ett virtuellt nätverk.

## <a name="why-use-a-managed-identity"></a>Varför använda en hanterad identitet?

Använd en hanterad identitet i en behållare som körs för att autentisera till alla [tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) utan att hantera autentiseringsuppgifter i behållarkoden. För tjänster som inte stöder AD-autentisering kan du lagra hemligheter i ett Azure-nyckelvalv och använda den hanterade identiteten för att komma åt nyckelvalvet för att hämta autentiseringsuppgifter. Mer information om hur du använder en hanterad identitet finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA). För närvarande stöds hanterade identiteter på Azure Container Instances endast med Linux-behållare och ännu inte med Windows-behållare.
>  

### <a name="enable-a-managed-identity"></a>Aktivera en hanterad identitet

 I Azure Container Instances stöds hanterade identiteter för Azure-resurser från och med REST API version 2018-10-01 och motsvarande SDK:er och verktyg. När du skapar en behållargrupp aktiverar du en eller flera hanterade identiteter genom att ange egenskapen [ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Du kan också aktivera eller uppdatera hanterade identiteter när en behållargrupp körs - antingen gör åtgärden att behållargruppen startar om. Om du vill ange identiteter för en ny eller befintlig behållargrupp använder du Azure CLI, en Resource Manager-mall eller en YAML-fil. 

Azure Container Instances stöder båda typerna av hanterade Azure-identiteter: användartilldelade och systemtilldelade. I en behållargrupp kan du aktivera en systemtilldelad identitet, en eller flera användartilldelade identiteter eller båda typerna av identiteter. 

* En **användartilldelad hanterad** identitet skapas som en fristående Azure-resurs i Azure AD-klienten som är betrodd av prenumerationen som används. När identiteten har skapats kan identiteten tilldelas en eller flera Azure-resurser (i Azure Container Instances eller andra Azure-tjänster). Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för behållargrupperna eller andra tjänstresurser som den har tilldelats. Det här beteendet är särskilt användbart i Azure Container Instances. Eftersom identiteten sträcker sig längre än livstiden för en behållargrupp kan du återanvända den tillsammans med andra standardinställningar för att göra distributionerna för behållargruppen mycket repeterbara.

* En **systemtilldelad hanterad** identitet aktiveras direkt på en behållargrupp i Azure Container Instances. När det är aktiverat skapar Azure en identitet för gruppen i Azure AD-klienten som är betrodd av prenumerationen på instansen. När identiteten har skapats etableras autentiseringsuppgifterna i varje behållare i behållargruppen. Livscykeln för en systemtilldelad identitet är direkt knuten till den behållargrupp som den är aktiverad på. När gruppen tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.

### <a name="use-a-managed-identity"></a>Använda en hanterad identitet

Om du vill använda en hanterad identitet måste identiteten först beviljas åtkomst till en eller flera Azure-tjänstresurser (till exempel en webbapp, ett nyckelvalv eller ett lagringskonto) i prenumerationen. Om du vill komma åt Azure-resurserna från en behållare som körs måste koden hämta en *åtkomsttoken* från en Azure AD-slutpunkt. Sedan skickar koden åtkomsttoken för ett samtal till en tjänst som stöder Azure AD-autentisering. 

Using a managed identity in a running container is essentially the same as using an identity in an Azure VM. Se vm-vägledningen för användning av en [token,](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) [Azure PowerShell eller Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)eller Azure [SDK: er](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure-nyckelvalv

Exemplen i den här artikeln använder en hanterad identitet i Azure Container Instances för att komma åt en hemlighet för Azure-nyckelvalv. 

Skapa först en resursgrupp med namnet *myResourceGroup* på platsen *eastus* med följande [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)-kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) för att skapa ett nyckelvalv. Var noga med att ange ett unikt nyckelvalvnamn. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Lagra ett exempelhemlighet i nyckelvalvet med kommandot [az keyvault secret set:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Fortsätt med följande exempel för att komma åt nyckelvalvet med antingen en användartilldelad eller systemtilldelad hanterad identitet i Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exempel 1: Använda en användartilldelad identitet för att komma åt Azure-nyckelvalvet

### <a name="create-an-identity"></a>Skapa en identitet

Skapa först en identitet i din prenumeration med kommandot [az identity create.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Du kan använda samma resursgrupp som används för att skapa nyckelvalvet eller använda ett annat.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Om du vill använda identiteten i följande steg använder du kommandot [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) för att lagra identitetens huvud-ID och resurs-ID för tjänsten i variabler.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Aktivera en användartilldelad identitet i en behållargrupp

Kör följande kommando [för az-behållarskap](/cli/azure/container?view=azure-cli-latest#az-container-create) för att `azure-cli` skapa en behållarinstans baserat på Microsofts avbildning. Det här exemplet innehåller en grupp med en behållare som du kan använda interaktivt för att köra Azure CLI för att komma åt andra Azure-tjänster. I det här avsnittet används endast det grundläggande Ubuntu-operativsystemet. 

Parametern `--assign-identity` skickar den användartilldelade hanterade identiteten till gruppen. Det långvariga kommandot håller behållaren igång. I det här exemplet används samma resursgrupp som används för att skapa nyckelvalvet, men du kan ange ett annat.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera dess status med kommandot [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Avsnittet `identity` i utdata liknar följande och visar att identiteten anges i behållargruppen. Under `principalID` `userAssignedIdentities` är tjänstens huvudnamn för den identitet som du skapade i Azure Active Directory:

```console
[...]
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
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Bevilja användartilldelade identitetsåtkomst till nyckelvalvet

Kör följande [az keyvault set-policy-kommando](/cli/azure/keyvault?view=azure-cli-latest) för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan den användartilldelade identiteten hämta hemligheter från nyckelvalvet:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Använd användartilldelade identitet för att få hemlighet från nyckelvalvet

Nu kan du använda den hanterade identiteten i den behållarinstans som körs för att komma åt nyckelvalvet. Starta först ett bash skal i behållaren:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kör följande kommandon i bash-skalet i behållaren. Om du vill hämta en åtkomsttoken för att använda Azure Active Directory för att autentisera till nyckelvalv kör du följande kommando:

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

Använd nu åtkomsttoken för att autentisera till nyckelvalvet och läsa en hemlighet. Var noga med att ersätta namnet på ditt nyckelvalv i webbadressen (*https:\//mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret liknar följande, visar hemligheten. I koden tolkar du den här utdata för att få hemligheten. Använd sedan hemligheten i en efterföljande åtgärd för att komma åt en annan Azure-resurs.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exempel 2: Använda en systemtilldelad identitet för att komma åt Azure-nyckelvalvet

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Aktivera en systemtilldelad identitet för en behållargrupp

Kör följande kommando [för az-behållarskap](/cli/azure/container?view=azure-cli-latest#az-container-create) för att `azure-cli` skapa en behållarinstans baserat på Microsofts avbildning. Det här exemplet innehåller en grupp med en behållare som du kan använda interaktivt för att köra Azure CLI för att komma åt andra Azure-tjänster. 

Parametern `--assign-identity` utan ytterligare värde möjliggör en systemtilldelad hanterad identitet för gruppen. Identiteten begränsas till resursgruppen för behållargruppen. Det långvariga kommandot håller behållaren igång. I det här exemplet används samma resursgrupp som används för att skapa nyckelvalvet, men du kan ange ett annat.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera dess status med kommandot [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Avsnittet `identity` i utdata liknar följande, som visar att en systemtilldelad identitet skapas i Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Ange en variabel till `principalId` värdet för (tjänstens huvud-ID) för identiteten, som ska användas i senare steg.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Bevilja åtkomst till behållargruppen till nyckelvalvet

Kör följande [az keyvault set-policy-kommando](/cli/azure/keyvault?view=azure-cli-latest) för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan den systemhanterade identiteten hämta hemligheter från nyckelvalvet:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Använd behållaregruppsidentitet för att få hemlighet från nyckelvalvet

Nu kan du använda den hanterade identiteten för att komma åt nyckelvalvet i den löphållna behållarinstansen. Starta först ett bash skal i behållaren:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kör följande kommandon i bash-skalet i behållaren. Logga först in på Azure CLI med den hanterade identiteten:

```bash
az login --identity
```

Hämta hemligheten från nyckelvalvet från behållaren som körs:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Värdet på hemligheten hämtas:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Aktivera hanterad identitet med hjälp av Resource Manager-mall

Om du vill aktivera en hanterad identitet i en behållargrupp `ContainerGroupIdentity` med hjälp av en Resource [Manager-mall](container-instances-multi-container-group.md)anger du egenskapen `identity` för `Microsoft.ContainerInstance/containerGroups` objektet med ett objekt. Följande utdrag visar egenskapen `identity` som konfigurerats för olika scenarier. Se [referensen för Resource Manager-mallen](/azure/templates/microsoft.containerinstance/containergroups). Ange ett `apiVersion` `2018-10-01`minimum av .

### <a name="user-assigned-identity"></a>Användartilldelad identitet

En användartilldelad identitet är ett resurs-ID i formuläret:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Du kan aktivera en eller flera användartilldelade identiteter.

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

### <a name="system--and-user-assigned-identities"></a>System- och användartilldelade identiteter

På en behållargrupp kan du aktivera både en systemtilldelad identitet och en eller flera användartilldelade identiteter.

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

Om du vill aktivera en hanterad identitet i en behållargrupp som distribueras med hjälp av en [YAML-fil](container-instances-multi-container-yaml.md)inkluderar du följande YAML.
Ange ett `apiVersion` `2018-10-01`minimum av .

### <a name="user-assigned-identity"></a>Användartilldelad identitet

En användartilldelad identitet är ett resurs-ID i formuläret 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Du kan aktivera en eller flera användartilldelade identiteter.

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

### <a name="system--and-user-assigned-identities"></a>System- och användartilldelade identiteter

På en behållargrupp kan du aktivera både en systemtilldelad identitet och en eller flera användartilldelade identiteter.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om hanterade identiteter i Azure Container Instances och hur du:

> [!div class="checklist"]
> * Aktivera en användartilldelad eller systemtilldelad identitet i en behållargrupp
> * Bevilja identitetsåtkomst till ett Azure-nyckelvalv
> * Använd den hanterade identiteten för att komma åt ett nyckelvalv från en behållare som körs

* Läs mer om [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/).

* Se ett [Azure Go SDK-exempel](https://medium.com/@samkreter/c98911206328) på hur du använder en hanterad identitet för att komma åt ett nyckelvalv från Azure Container Instances.
