---
title: Snabbstart - Azure Key Vault-klientbibliotek för Node.js (v4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med nod.js-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: bda51b71ff1e6c89414bd1540bb38dcfea1d4a3c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457227"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Snabbstart: Azure Key Vault-klientbibliotek för Node.js (v4)

Kom igång med Azure Key Vault-klientbiblioteket för Node.js. Följ stegen nedan för att installera paketet och prova exempelkod för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd klientbiblioteket Key Vault för Node.js för att:

- Öka säkerheten och kontrollen över nycklar och lösenord.
- Skapa och importera krypteringsnycklar på några minuter.
- Minska svarstiden med molnskala och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 Nivå 2 validerade HSM.Use FIPS 140-2 Level 2 validated HSMs.

[API-referensdokumentation](/javascript/api/overview/azure/key-vault?view=azure-node-latest) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [(npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aktuella [nod.js](https://nodejs.org) för ditt operativsystem.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabbstarten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Inrätta

### <a name="install-the-package"></a>Installera paketet

Installera azure key vault-hemlighetsbiblioteket för Node.js i konsolfönstret.

```console
npm install @azure/keyvault-secrets
```

För den här snabbstarten måste du också installera azure.identity-paketet:

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och nyckelvalv

Den här snabbstarten använder ett förskapade Azure-nyckelvalv. Du kan skapa ett nyckelvalv genom att följa stegen i [snabbstarten Azure CLI,](quick-create-cli.md) [Snabbstarten i Azure PowerShell](quick-create-powershell.md)eller [Snabbstarten för Azure-portalen](quick-create-portal.md). Alternativt kan du helt enkelt köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckelvalv måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserat program är med en hanterad identitet. Se [Använda en hanterad apptjänstidentitet för att komma åt Azure Key Vault](managed-identity.md) för mer information. För enkelhetens skull skapar dock den här snabbstarten ett konsolprogram. Autentisering av ett skrivbordsprogram med Azure kräver användning av ett tjänsthuvudnamn och en åtkomstkontrollprincip.

Skapa en tjänstprincip med kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Denna operation kommer att returnera en serie nyckel / värdepar. 

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

Notera clientId och clientSecret, eftersom vi kommer att använda dem i [steget Ange miljövariabel](#set-environmental-variables) nedan.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvudnamn åtkomst till nyckelvalvet

Skapa en åtkomstprincip för nyckelvalvet som ger behörighet till tjänstens huvudnamn genom att skicka clientId till kommandot [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Ge tjänstens huvudnamn behörigheter, lista och ange behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ange miljövariabler

Metoden DefaultAzureCredential i vår applikation bygger på `AZURE_CLIENT_ID`tre `AZURE_CLIENT_SECRET`miljövariabler: , och `AZURE_TENANT_ID`. Ange dessa variabler till klientId-, clientSecret- och tenantId-värden som `export VARNAME=VALUE` du noterade i steget Skapa ett [tjänsthuvudnamn](#create-a-service-principal) med formatet. (Detta anger bara variablerna för det aktuella skalet och processer som skapats från `/etc/environment ` skalet. 

Du måste också spara nyckelvalvets namn som `KEY_VAULT_NAME`en miljövariabel som kallas .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault-klientbiblioteket för Node.js kan du hantera nycklar och relaterade resurser som certifikat och hemligheter. Kodexemplen nedan visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsolappen finns https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-apppå .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande direktiv överst i koden:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Att autentisera till ditt nyckelvalv och skapa en nyckelvalvklient beror på miljövariablerna från steget [Ange miljövariabler](#set-environmental-variables) ovan och [SecretClient-konstruktorn](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Namnet på nyckelvalvet expanderas till nyckelvalvet URI, i formatet `https://<your-key-vault-name>.vault.azure.net`. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du placera en hemlighet i din keyvault med metoden [client.setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) Detta kräver ett namn för hemligheten – vi använder "mySecret" i det här exemplet.  

```javascript
await client.setSecret(secretName, secretValue);
```

Du kan kontrollera att hemligheten har angetts med kommandot [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare inställda värdet med [metoden client.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Din hemlighet är `retrievedSecret.value`nu sparad som .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Låt oss slutligen ta bort hemligheten från nyckelvalvet med [metoden client.beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

Du kan kontrollera att hemligheten är borta med [az keyvault hemliga visa](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) kommandot:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckelvalvet och motsvarande resursgrupp.

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

I den här snabbstarten skapade du ett nyckelvalv, lagrade en hemlighet och hämtade den hemligheten. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs en [översikt över Azure Key Vault](key-vault-overview.md)
- Se [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md)
- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [metodtips för Azure Key Vault](key-vault-best-practices.md)