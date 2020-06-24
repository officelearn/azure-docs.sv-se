---
title: Snabb start – Azure Key Vault klient bibliotek för Node.js (v4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av Node.js klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 21add865818d73937aec241f8f60e20158202348
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125301"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Snabb start: Azure Key Vault klient bibliotek för Node.js (v4)

Kom igång med Azure Key Vault klient biblioteket för Node.js. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault klient biblioteket för Node.js för att:

- Öka säkerheten och kontrollen över nycklar och lösen ord.
- Skapa och importera krypterings nycklar på några minuter.
- Minska svars tiden med moln skalning och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 nivå 2-verifierade HSM: er.

[API-referens dokumentation](/javascript/api/overview/azure/key-vault?view=azure-node-latest)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nuvarande [Node.js](https://nodejs.org) för ditt operativ system.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-package"></a>Installera paketet

I konsol fönstret installerar du biblioteket Azure Key Vault hemligheter för Node.js.

```console
npm install @azure/keyvault-secrets
```

I den här snabb starten måste du även installera Azure. Identity-paketet:

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

I den här snabb starten används ett i förväg skapade Azure Key Vault. Du kan skapa ett nyckel valv genom att följa stegen i snabb starten för [Azure CLI](quick-create-cli.md), [Azure PowerShell snabb start](quick-create-powershell.md)eller [Azure Portal snabb start](quick-create-portal.md). Alternativt kan du bara köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserad program är med en hanterad identitet. Mer information finns i [använda en app service hanterad identitet för att få åtkomst till Azure Key Vault](../general/managed-identity.md) .

För enkelhetens skull skapar den här snabb starten ett Skriv bords program som kräver användning av ett huvud namn för tjänsten och en princip för åtkomst kontroll. Tjänstens huvud namn kräver ett unikt namn i formatet "http:// &lt; My-Unique-service-huvud namn &gt; ".

Skapa ett huvud namn för tjänsten med hjälp av Azure CLI [-AZ AD SP Create-for-RBAC-](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommandot:

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Den här åtgärden returnerar en serie med nyckel/värde-par. 

```azurecli
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Anteckna clientId och clientSecret, eftersom vi kommer att använda dem i steget [Ange miljö variabel](#set-environmental-variables) .

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger behörighet till tjänstens huvud namn genom att skicka clientId till [AZ-kommandot Set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Ge tjänstens huvud namn get-, list-och set-behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ange miljövariabler

DefaultAzureCredential-metoden i programmet är beroende av tre miljövariabler: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` och `AZURE_TENANT_ID` . Ange dessa variabler som clientId-, clientSecret-och tenantId-värden som du antecknade i steget [skapa ett huvud namn för tjänsten](#create-a-service-principal) med hjälp av `export VARNAME=VALUE` formatet. (Detta anger bara variabler för ditt aktuella gränssnitt och processer som skapats från gränssnittet. om du vill lägga till dessa variabler permanent i miljön redigerar du `/etc/environment ` filen.) 

Du måste också spara nyckel valvets namn som en miljö variabel som kallas `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objekt modell

Med Azure Key Vault klient biblioteket för Node.js kan du hantera nycklar och relaterade till gångar som certifikat och hemligheter. I kod exemplen nedan visas hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsol programmet finns på https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Autentisering till ditt nyckel valv och att skapa en Key Vault-klient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan och SecretClient- [konstruktorn](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Namnet på nyckel valvet expanderas till Key Vault-URI: n i formatet `https://<your-key-vault-name>.vault.azure.net` . 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du ange en hemlighet i ditt nyckel valv med hjälp av [metoden client. setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) som kräver ett namn för hemligheten – vi använder "hemligheter" i det här exemplet.  

```javascript
await client.setSecret(secretName, secretValue);
```

Du kan kontrol lera att hemligheten har angetts med kommandot [AZ-valvets hemliga show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare angivna värdet med [metoden client. getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Din hemlighet sparas nu som `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort hemligheten från nyckel valvet med [metoden client. beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

Du kan kontrol lera att hemligheten är borta med kommandot [AZ-valv för hemligt show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckel valvet och motsvarande resurs grupp.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exempelkod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet och hämtat hemligheten. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)