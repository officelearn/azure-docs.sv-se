---
title: Självstudie – använda Azure Key Vault med en Azure webapp i .NET | Microsoft Docs
description: I den här självstudien konfigurerar du och Azure webapp i ett ASP.NET Core-program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4ed999e282aa9bcd80b000f3db2ecf9a8386a489
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537959"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Självstudie: Använd en hanterad identitet för att ansluta Key Vault till en Azure-webbapp med .NET

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) är ett sätt att lagra autentiseringsuppgifter på ett säkert sätt och andra hemligheter, men koden måste autentiseras för att Key Vault ska kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../../active-directory/managed-identities-azure-resources/overview.md) löser detta problem genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva Visa autentiseringsuppgifter i din kod.

I den här självstudien används en hanterad identitet för att autentisera en Azure-webbapp med en Azure Key Vault. Även om stegen använder [Azure Key Vault v4-klient biblioteket för .net](/dotnet/api/overview/azure/key-vault) och [Azure CLI](/cli/azure/get-started-with-azure-cli), gäller samma grundläggande principer när du använder det utvecklings språk du väljer, Azure PowerShell och/eller Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Installera git](https://www.git-scm.com/downloads).
* [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/)
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview). Du kan skapa nyckel valv med hjälp av [Azure Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)eller [Azure PowerShell](quick-create-powershell.md).
* Key Vault [hemlighet](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets). Du kan skapa hemlighet med [Azure Portal](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal), [POWERSHELL](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)eller [Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)

## <a name="create-a-net-core-app-and-deploy-it-to-azure"></a>Skapa en .NET Core-app och distribuera den till Azure
I det här steget konfigurerar du det lokala .NET Core-projektet.

I ett terminalfönster på datorn skapar du en katalog med namnet `akvwebapp` och ändrar katalogen till den.

```bash
mkdir akvwebapp
cd akvwebapp
```

Skapa nu en ny .NET Core-app med kommandot [dotNet New Web](/dotnet/core/tools/dotnet-new) :

```bash
dotnet new web
```

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. 

```bash
dotnet run
```

Öppna webbläsaren och navigera till appen på `http://localhost:5000`.

Du ser **Hello World** meddelandet från exempel programmet som visas på sidan.

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du ditt .NET Core-program till App Service med hjälp av lokal git. Mer information om hur du skapar och distribuerar program finns i [skapa en ASP.net Core webbapp i Azure](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore)

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.  Initiera en Git-lagringsplats för .NET Core-projektet.

```bash
git init
git add .
git commit -m "first commit"
```

FTP och lokal git kan distribueras till en Azure-webbapp med hjälp av en *distributions användare*. När du har konfigurerat din distributions användare kan du använda den för alla dina Azure-distributioner. Ditt användar namn och lösen ord för distribution på konto nivå skiljer sig från dina autentiseringsuppgifter för Azure-prenumerationen. 

Konfigurera distributions användaren genom att köra kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) . Välj ett användar namn och lösen ord som följer dessa rikt linjer: 

- Användar namnet måste vara unikt inom Azure, och för lokala git-push-meddelanden får inte innehålla symbolen @. 
- Lösen ordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON-utdata visar lösen ordet som `null` . Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användar namn och lösen ord som ska användas för att distribuera dina webb program.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resurs grupp för att House både ditt nyckel valv och din webbapp med kommandot [AZ Group Create](/cli/azure/group?#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Skapa en app service-plan

Skapa en [App Service plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) med kommandot Azure CLI [AZ AppService plan Create](/cli/azure/appservice/plan) . I följande exempel skapas en App Service plan med namnet `myAppServicePlan` på den **kostnads fria** pris nivån:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Mer information om App Service plan hantering finns i [hantera ett App Service plan i Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en [Azure-webbapp](../../app-service/overview.md) i `myAppServicePlan` App Service plan. 

> [!Important]
> I likhet med Key Vault måste en Azure-webbapp ha ett unikt namn. Ersätt \<your-webapp-name\> med namnet på din webbapp med följande exempel.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Spara den här URL: en när du behöver den senare.

Bläddra till appen som precis skapades. Ersätt _&lt; ditt-webapp-namn>_ med ditt app-namn.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Standard webb sidan visas för en nyligen skapad Azure-webbapp.

### <a name="deploy-your-local-app"></a>Distribuera din lokala app

I det lokala terminalfönstret lägger du till en Azure-fjärrdatabas till din lokala git-lagringsplats, ersätter *\<deploymentLocalGitUrl-from-create-step>* med URL: en för den git-fjärrdatabas som du sparade från [skapa ett webb program](#create-a-web-app) steg.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När git Credential Manager uppmanas att ange autentiseringsuppgifter använder du de autentiseringsuppgifter som du skapade i steget [Konfigurera lokal Git-distribution](#configure-local-git-deployment) .

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Bläddra till (eller uppdatera) det distribuerade programmet med hjälp av webbläsaren.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Du kommer att se "Hello World!" meddelande som du tidigare såg när du besökte `http://localhost:5000` .
 
## <a name="configure-web-app-to-connect-to-key-vault"></a>Konfigurera webb program för att ansluta till Key Vault

I det här avsnittet ska du konfigurera webb åtkomst till Key Vault och uppdatera program koden för att hämta hemlighet från Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Skapa och tilldela en hanterad identitet

I den här självstudien ska vi använda [Programhanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för att autentisera till nyckel valv, som automatiskt hanterar programautentiseringsuppgifter.

I Azure CLI skapar du identiteten för det här programmet genom att köra kommandot [AZ webapp-Identity Assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Åtgärden returnerar följande JSON-kodfragment:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Om du vill ge ditt webb program behörighet att utföra **Get** -och **list** -åtgärder i ditt nyckel valv skickar du principalID till Azure CLI [-AZ för att ange princip](/cli/azure/keyvault?#az-keyvault-set-policy) kommando:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Du kan också tilldela åtkomst principer med [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) eller [PowerShell](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell).

### <a name="modify-the-app-to-access-your-key-vault"></a>Ändra appen för åtkomst till ditt nyckel valv

#### <a name="install-the-packages"></a>Installera paketen

I terminalfönstret installerar du Azure Key Vault klient biblioteket för .NET-paket:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Uppdatera koden

Hitta och öppna filen Startup.cs i ditt akvwebapp-projekt. 

Lägg till de här två raderna i rubriken:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Lägg till de här raderna före `app.UseEndpoints` anropet, och uppdatera URI: n för att avspegla `vaultUri` nyckel valvet. I koden nedan används  ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) för autentisering till Key Vault, som använder token från Programhanterad identitet för att autentisera. Mer information om autentisering till Key Vault finns i [Developer ' s guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code). Den använder också exponentiell backoff för återförsök om nyckel valvet begränsas. Mer information om transaktions gränser för Key Vault finns i [rikt linjer för läs Azure Key Vault begränsning](https://docs.microsoft.com/azure/key-vault/general/overview-throttling)

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Uppdatera raden `await context.Response.WriteAsync("Hello World!");` för att läsa:

```csharp
await context.Response.WriteAsync(secretValue);
```

Var säker på att spara ändringarna innan du fortsätter till nästa steg.

#### <a name="redeploy-your-web-app"></a>Distribuera din webbapp igen

När du har uppdaterat koden kan du distribuera om den till Azure med följande git-kommandon:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

### <a name="visit-your-completed-web-app"></a>Besök din färdiga webbapp

```bash
http://<your-webapp-name>.azurewebsites.net
```

Innan du såg **Hello World** bör du nu se värdet för din hemlighet som visas: **lyckades!**

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Key Vault med program som distribueras till en virtuell dator i .NET](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- Lär dig mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
- Läs mer om [hanterade identiteter för App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- [Utvecklarguide](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [Säker åtkomst till ett nyckel valv](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


