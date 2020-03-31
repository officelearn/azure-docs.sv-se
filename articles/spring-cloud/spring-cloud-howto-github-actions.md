---
title: Azure Spring Cloud CI/CD med GitHub-åtgärder
description: Så här bygger du upp CI/CD-arbetsflöde för Azure Spring Cloud med GitHub-åtgärder
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538472"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD med GitHub-åtgärder

GitHub-åtgärder stöder ett automatiskt livscykelarbetsflöde för programvaruutveckling. Med GitHub-åtgärder för Azure Spring Cloud kan du skapa arbetsflöden i din databas för att skapa, testa, paketera, släppa och distribuera till Azure. 

## <a name="prerequisites"></a>Krav
Det här exemplet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Konfigurera GitHub-databasen och autentisera
Du behöver en Azure-principautentisering för att godkänna Azure-inloggningsåtgärd. Om du vill hämta en Azure-autentiseringsuppgifter kör du följande kommandon på din lokala dator:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Om du vill komma åt en viss resursgrupp kan du minska omfattningen:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Kommandot ska mata ut ett JSON-objekt:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

I det här exemplet används exemplet [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) på GitHub.  Öppna GitHub-databassidan och klicka på fliken **Inställningar.** **Secrets** **Add a new secret**

 ![Lägg till ny hemlighet](./media/github-actions/actions1.png)

Ange det hemliga namnet till `AZURE_CREDENTIALS` och dess värde till den JSON-sträng som du hittade under rubriken Konfigurera din *GitHub-databas och autentisera*.

 ![Ange hemliga data](./media/github-actions/actions2.png)

Du kan också hämta Azure-inloggningsautentiseringsuppgifterna från Key Vault i GitHub-åtgärder som förklaras i [Authenticate Azure Spring med Key Vault i GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Tjänstinstans för etablering
Om du vill etablera din Azure Spring Cloud-tjänstinstans kör du följande kommandon med Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Skapa arbetsflödet
Arbetsflödet definieras med hjälp av följande alternativ.

### <a name="prepare-for-deployment-with-azure-cli"></a>Förbered för distribution med Azure CLI
Kommandot `az spring-cloud app create` är för närvarande inte idempotent.  Vi rekommenderar det här arbetsflödet för befintliga Azure Spring Cloud-appar och instanser.

Använd följande Azure CLI-kommandon för förberedelse:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuera med Azure CLI direkt
Skapa `.github/workflow/main.yml` filen i databasen:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Distribuera med Azure CLI-åtgärd
Kommandot `run` az använder den senaste versionen av Azure CLI. Om det finns bryta ändringar kan du också använda en `action`specifik version av Azure CLI med azure/CLI . 

> [!Note] 
> Det här kommandot körs i `env` en ny behållare, så fungerar inte, och åtkomst mellan åtgärder kan ha extra begränsningar.

Skapa filen .github/workflow/main.yml i databasen:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Distribuera med Maven Plugin
Ett annat alternativ är att använda [Maven Plugin](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) för att distribuera Jar och uppdatera App inställningar. Kommandot `mvn azure-spring-cloud:deploy` är idempotent och kommer automatiskt att skapa appar om det behövs. Du behöver inte skapa motsvarande appar i förväg.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Kör arbetsflödet
**GitHub-åtgärder** ska aktiveras automatiskt `.github/workflow/main.yml` när du har pushat till GitHub. Åtgärden utlöses när du trycker på ett nytt åtagande. Om du skapar den här filen i webbläsaren bör åtgärden redan ha körts.

Om du vill kontrollera att åtgärden har aktiverats klickar du på fliken **Åtgärder** på GitHub-databassidan:

 ![Verifiera åtgärden aktiverad](./media/github-actions/actions3.png)

Om åtgärden körs av misstag, till exempel om du inte har angett Azure-autentiseringsuppgifter, kan du köra checkar igen när du har åtgärdat felet. Klicka på **Åtgärder**på GitHub-databassidan, välj den specifika arbetsflödesuppgiften och klicka sedan på knappen **Kör om kontroller** för att köra checkar igen:

 ![Kör om kontroller](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Nästa steg
* [Key Vault för GitHub-åtgärder i vårmoln](./spring-cloud-github-actions-key-vault.md)
* [Huvudnamn för Azure Active Directory-tjänsten](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub-åtgärder för Azure](https://github.com/Azure/actions/)
