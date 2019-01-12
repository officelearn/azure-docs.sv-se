---
title: Använda en hanterad identitet med Azure Container Instances
description: Lär dig hur du använder en hanterad identitet för att autentisera med andra Azure-tjänster från Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: b46539758d88fe7a0e27799b5da581255fa5f075
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229340"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Hur du använder hanterade identiteter med Azure Container Instances

Använd [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) att köra kod i Azure Container Instances som samverkar med andra Azure-tjänster – utan att behålla alla hemligheter eller autentiseringsuppgifter i kod. Funktionen ger ett Azure Container Instances-distribution med en automatiskt hanterad identitet i Azure Active Directory.

I den här artikeln får du lära dig mer om hanterade identiteter i Azure Container Instances och:

> [!div class="checklist"]
> * Aktivera en Användartilldelad eller systemtilldelade identiteter i en behållargrupp
> * Bevilja identity-åtkomst till ett Azure Key Vault
> * Använda den hanterade identitet för åtkomst till Key Vault från en behållare som körs

Anpassa exemplen för att aktivera och använda identiteter i Azure Container Instances för att få åtkomst till andra Azure-tjänster. De här exemplen är interaktiva. I praktiken men körs dina behållaravbildningar koden för att komma åt Azure-tjänster.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

Använda en hanterad identitet i en behållare som körs för att autentisera till någon [tjänst som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication) utan att hantera autentiseringsuppgifter i din kod för behållaren. För tjänster som inte stöder AD-autentisering, kan du lagra hemligheter i Azure Key Vault och använda den hanterade identitet för åtkomst till Key Vault för att hämta autentiseringsuppgifter. Läs mer om hur du använder en hanterad identitet, [vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA). För närvarande stöds endast hanterade identiteter på Linux-behållarinstanser.
>  

### <a name="enable-a-managed-identity"></a>Aktivera en hanterad identitet

 I Azure Container Instances stöds hanterade identiteter för Azure-resurser från och med REST API-versionen 2018-10-01 och motsvarande SDK: er och verktyg. När du skapar en behållargrupp kan du aktivera en eller flera hanterade identiteter genom att ange en [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) egenskapen. Du kan också aktivera eller uppdatera hanterade identiteter efter en behållargrupp körs på. antingen medför behållargruppen startas om. Ange identiteter på en ny eller befintlig behållargrupp med hjälp av Azure CLI, Resource Manager-mall eller en YAML-fil. 

Azure Container Instances stöder båda typerna av hanterade Azure-identiteter: användartilldelade och systemtilldelade. Du kan aktivera en systemtilldelad identitet, en eller flera användartilldelade identiteter eller båda typerna av identiteter på en behållargrupp. 

* En **Användartilldelad** hanterad identitet skapas som en fristående Azure-resurser i Azure AD-klient som är betrodd av prenumerationen som används. När identiteten har skapats kan identiteten tilldelas till en eller flera Azure-resurser (i Azure Container Instances eller andra Azure-tjänster). Livscykeln för en Användartilldelad identitet hanteras separat från livscykeln för behållargrupper eller övriga resurser som den är tilldelad. Detta är särskilt användbart i Azure Container Instances. Eftersom identiteten sträcker sig utanför livslängden för en behållargrupp, kan du återanvända den tillsammans med andra standardinställningar för att göra din grupp behållardistributioner med hög repeterbara.

* En **systemtilldelade** hanterad identitet aktiveras direkt på en behållargrupp i Azure Container Instances. När den är aktiverad skapar Azure en identitet för gruppen i Azure AD-klient som är betrodd av prenumerationen för instansen. När identiteten har skapats kan etableras autentiseringsuppgifterna i varje behållare i behållargruppen. Livscykeln för en systemtilldelad identitet är direkt kopplad till behållargruppen som det är aktiverat på. När gruppen tas bort, rensas Azure automatiskt autentiseringsuppgifterna och identitet i Azure AD.

### <a name="use-a-managed-identity"></a>Använda en hanterad identitet

Om du vill använda en hanterad identitet, måste identiteten först beviljas åtkomst till en eller flera Azure-tjänstresurser (till exempel en Webbapp, ett Key Vault eller ett Lagringskonto) i prenumerationen. Om du vill komma åt Azure-resurser från en fungerande behållare måste din kod måste skaffa ett *åtkomsttoken* från en Azure AD-slutpunkten. Koden skickar sedan åtkomsttoken på ett anrop till en tjänst som stöder Azure AD-autentisering. 

Med hjälp av en hanterad identitet i en behållare som körs är i princip detsamma som att använda en identitet i en Azure VM. Se VM-riktlinjer för att använda en [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell eller Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), eller [Azure SDK: er](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

I exemplen i den här artikeln används en hanterad identitet i Azure Container Instances för att få åtkomst till en Azure Key Vault-hemlighet. 

Skapa först en resursgrupp med namnet *myResourceGroup* i den *eastus* plats med följande [az gruppen skapa](/cli/azure/group?view=azure-cli-latest#az-group-create) kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd den [az keyvault skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) kommando för att skapa ett Nyckelvalv. Glöm inte att ange ett unikt namn för Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store en exempel-hemlighet i Key Vault med den [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) kommando:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Fortsätt med följande exempel för att få åtkomst till Key Vault med hjälp av antingen en Användartilldelad eller systemtilldelade hanterad identitet i Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exempel 1: Använda en Användartilldelad identitet för åtkomst till Azure Key Vault

### <a name="create-an-identity"></a>Skapa en identitet

Skapa först en identitet i din prenumeration med hjälp av den [az identitet skapa](/cli/azure/identity?view=azure-cli-latest#az-identity-create) kommando. Du kan använda samma resursgrupp som används för att skapa Key Vault eller använda ett annat namn.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Om du vill använda identiteten i följande steg, använder den [az identitet show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) kommando för att lagra Identitetens ID för tjänstens huvudnamn och resurs-ID i variabler.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Aktivera en Användartilldelad identitet på en behållargrupp

Kör följande [az container skapa](/cli/azure/container?view=azure-cli-latest#az-container-create) kommando för att skapa en behållarinstans utifrån Ubuntu Server. Det här exemplet innehåller en enskild behållare-grupp som du kan använda interaktiv åtkomst till andra Azure-tjänster. Den `--assign-identity` parametern skickar din hanterade Användartilldelad identitet till gruppen. Kommandot tidskrävande behåller den behållare som körs. Det här exemplet används samma resursgrupp som används för att skapa Key Vault, men du kan ange ett annat namn.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera statusen med den [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) kommando.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Den `identity` avsnittet i utdata ser ut ungefär så här, som visar identitet har angetts i behållargruppen. Den `principalID` under `userAssignedIdentities` är tjänstens huvudnamn med det identitet som du skapade i Azure Active Directory:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Bevilja användartilldelade identiteter åtkomst till Key Vault

Kör följande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) kommandot för att definiera en åtkomstprincip för Key Vault. I följande exempel kan Användartilldelad identitet att hämta hemligheter från Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Använd Användartilldelad identitet för att hämta en hemlighet från Key Vault

Nu kan du använda den hanterade identitet för åtkomst till Key Vault inom den container-instansen som körs. Starta först ett bash-gränssnitt i behållaren i det här exemplet:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i behållaren. Om du vill få en åtkomsttoken som du använder Azure Active Directory för att autentisera till Key Vault, kör du följande kommando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Utdata:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

För att lagra den åtkomst-token i en variabel som ska användas i efterföljande kommandon för att autentisera, kör du följande kommando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Nu Använd åtkomsttoken för att autentisera till Key Vault och läsa en hemlighet. Se till att ersätta namnet på ditt nyckelvalv i URL-Adressen (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret liknar följande, som visar hemligheten. I koden, skulle du analysera den här utdata för att hämta hemligheten. Använd sedan hemligheten i en åtgärd för efterföljande åtkomst till en annan Azure-resurs.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exempel 2: Använda en systemtilldelad identitet för åtkomst till Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Aktivera en systemtilldelade identiteter på en behållargrupp

Kör följande [az container skapa](/cli/azure/container?view=azure-cli-latest#az-container-create) kommando för att skapa en behållarinstans utifrån Ubuntu Server. Det här exemplet innehåller en enskild behållare-grupp som du kan använda interaktiv åtkomst till andra Azure-tjänster. Den `--assign-identity` med inga ytterligare värde för parametern gör det möjligt för en automatiskt genererad hanterad identitet för gruppen. Kommandot tidskrävande behåller den behållare som körs. Det här exemplet används samma resursgrupp som används för att skapa Key Vault, men du kan ange ett annat namn.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera statusen med den [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) kommando.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Den `identity` avsnittet i utdata ser ut ungefär så här, som visar att en systemtilldelad identitet har skapats i Azure Active Directory:

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

Ange en variabel till värdet för `principalId` (ID för tjänstens huvudnamn) för identitet, att använda i senare steg.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Ge åtkomst till behållaren till Key Vault

Kör följande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) kommandot för att definiera en åtkomstprincip för Key Vault. I följande exempel kan hanteras av datorn identiteten att hämta hemligheter från Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Använd behållare grupp identitet för att hämta en hemlighet från Key Vault

Nu kan du använda den hanterade identitet för åtkomst till Key Vault inom den container-instansen som körs. Starta först ett bash-gränssnitt i behållaren i det här exemplet:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Kör följande kommandon i bash-gränssnittet i behållaren. Om du vill få en åtkomsttoken som du använder Azure Active Directory för att autentisera till Key Vault, kör du följande kommando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Utdata:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

För att lagra den åtkomst-token i en variabel som ska användas i efterföljande kommandon för att autentisera, kör du följande kommando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Nu Använd åtkomsttoken för att autentisera till Key Vault och läsa en hemlighet. Se till att ersätta namnet på ditt nyckelvalv i URL-Adressen (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Svaret liknar följande, som visar hemligheten. I koden, skulle du analysera den här utdata för att hämta hemligheten. Använd sedan hemligheten i en åtgärd för efterföljande åtkomst till en annan Azure-resurs.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Aktivera hanterad identitet med hjälp av Resource Manager-mall

Så här aktiverar du en hanterad identitet i en behållare med en [Resource Manager-mall](container-instances-multi-container-group.md), ange den `identity` egenskapen för den `Microsoft.ContainerInstance/containerGroups` objekt med en `ContainerGroupIdentity` objekt. Följande kodavsnitt visar den `identity` egenskapen som har konfigurerats för olika scenarier. Se den [mallreferensen Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Ange en `apiVersion` av `2018-10-01`.

### <a name="user-assigned-identity"></a>Användartilldelad identitet

En Användartilldelad identitet är ett resurs-ID i formatet:

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

### <a name="system-assigned-identity"></a>Systemtilldelade identiteter

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>System - och användartilldelade identiteter

Du kan aktivera både en systemtilldelad identitet och en eller flera användartilldelade identiteter på en behållargrupp.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Aktivera hanterad identitet med hjälp av YAML-fil

Så här aktiverar du en hanterad identitet i en behållargrupp distribueras med en [YAML-fil](container-instances-multi-container-yaml.md), inkluderar följande YAML.
Ange en `apiVersion` av `2018-10-01`.

### <a name="user-assigned-identity"></a>Användartilldelad identitet

En Användartilldelad identitet är ett resurs-ID i formatet 

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

### <a name="system-assigned-identity"></a>Systemtilldelade identiteter

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>System - och användartilldelade identiteter

Du kan aktivera både en systemtilldelad identitet och en eller flera användartilldelade identiteter på en behållargrupp.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hanterade identiteter i Azure Container Instances och hur du:

> [!div class="checklist"]
> * Aktivera en Användartilldelad eller systemtilldelade identiteter i en behållargrupp
> * Bevilja identity-åtkomst till ett Azure Key Vault
> * Använda den hanterade identitet för åtkomst till Key Vault från en behållare som körs

* Läs mer om [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/).

* Se en [Azure Go SDK exempel](https://medium.com/@samkreter/c98911206328) med att använda en hanterad identitet för att komma åt ett Nyckelvalv från Azure Container Instances.