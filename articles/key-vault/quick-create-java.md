---
title: Snabbstart - Azure Key Vault-klientbibliotek för Java
description: Innehåller format- och innehållsvillkor för att skriva snabbstarter för Azure SDK-klientbibliotek.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: fd2373eb1c5cd5b48651c9215b5d6ad85c7acd68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457278"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Snabbstart: Azure Key Vault-klientbibliotek för Java

Kom igång med Azure Key Vault-klientbiblioteket för Java. Följ stegen nedan för att installera paketet och prova exempelkod för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault-klientbiblioteket för Java för att:

- Öka säkerheten och kontrollen över nycklar och lösenord.
- Skapa och importera krypteringsnycklar på några minuter.
- Minska svarstiden med molnskala och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 Nivå 2 validerade HSM.Use FIPS 140-2 Level 2 validated HSMs.

[Source code](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [Referensdokumentation](https://azure.github.io/azure-sdk-for-java) | för[källkods-API Produktdokumentation](index.yml) | [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 eller senare
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabbstarten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) och [Apache Maven](https://maven.apache.org) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Inrätta

### <a name="create-new-java-console-app"></a>Skapa en ny Java-konsolapp

I ett konsolfönster `mvn` använder du kommandot för att skapa `akv-java`en ny Java-konsolapp med namnet .

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Resultatet från att generera projektet kommer att se ut ungefär så här:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Ändra din katalog till den nyskapade akv-java/ mappen.

```console
cd akv-java
```

### <a name="install-the-package"></a>Installera paketet

Öppna *filen pom.xml* i textredigeraren. Lägg till följande beroendeelement i beroendegruppen.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och nyckelvalv

Den här snabbstarten använder ett förskapade Azure-nyckelvalv. Du kan skapa ett nyckelvalv genom att följa stegen i [snabbstarten Azure CLI,](quick-create-cli.md) [Snabbstarten i Azure PowerShell](quick-create-powershell.md)eller [Snabbstarten för Azure-portalen](quick-create-portal.md). Alternativt kan du köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckelvalv måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserat program är med en hanterad identitet. Se [Använda en hanterad apptjänstidentitet för att komma åt Azure Key Vault](managed-identity.md) för mer information. För enkelhetens skull skapar dock den här snabbstarten ett skrivbordsprogram som kräver användning av ett tjänsthuvudnamn och en åtkomstkontrollprincip.

Skapa en tjänstprincip med kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Denna operation kommer att returnera en serie nyckel / värdepar. 

```console
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

Notera clientId, clientSecret och tenantId, eftersom vi kommer att använda dem i de kommande två stegen.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvudnamn åtkomst till nyckelvalvet

Skapa en åtkomstprincip för nyckelvalvet som ger behörighet till tjänstens huvudnamn genom att skicka clientId till kommandot [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Ge tjänstens huvudnamn behörigheter, lista och ange behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ange miljövariabler

Metoden DefaultAzureCredential i vår applikation bygger på `AZURE_CLIENT_ID`tre `AZURE_CLIENT_SECRET`miljövariabler: , och `AZURE_TENANT_ID`. Använd ange dessa variabler till klientId-, clientSecret- och tenantId-värden som du noterade i steget [Skapa en tjänsthuvudnamn](#create-a-service-principal) ovan. Använd `export VARNAME=VALUE` formatet för att ställa in dina miljövariabler. (Den här metoden anger bara variablerna för det aktuella skalet och processer som skapats `/etc/environment ` från skalet. 

Du måste också spara nyckelvalvets namn som `KEY_VAULT_NAME`en miljövariabel som kallas .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault-klientbiblioteket för Java kan du hantera nycklar och relaterade resurser som certifikat och hemligheter. Kodexemplen nedan visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsolappen finns [nedan](#sample-code).

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande direktiv överst i koden:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Att autentisera till ditt nyckelvalv och skapa en nyckelvalvklient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan. Namnet på nyckelvalvet expanderas till nyckelvalvet URI, i formatet `https://<your-key-vault-name>.vault.azure.net`.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du lägga `secretClient.setSecret` en hemlighet i din keyvault med hjälp av metoden. Detta kräver ett namn för hemligheten - vi har tilldelat värdet `secretName` "mySecret" till variabeln i det här exemplet.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Du kan kontrollera att hemligheten har angetts med kommandot [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det `secretClient.getSecret` tidigare inställda värdet med metoden.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Du kan nu komma åt värdet `retrievedSecret.getValue()`för den hämtade hemligheten med .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen, låt oss ta bort hemligheten `secretClient.beginDeleteSecret` från din nyckel valv med metoden.

```java
secretClient.beginDeleteSecret(secretName);
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

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade en hemlighet och hämtade den hemligheten. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs en [översikt över Azure Key Vault](key-vault-overview.md)
- Se [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md)
- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [metodtips för Azure Key Vault](key-vault-best-practices.md)
