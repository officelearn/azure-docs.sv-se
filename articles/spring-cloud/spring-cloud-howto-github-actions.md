---
title: Azure våren Cloud CI/CD med GitHub-åtgärder
description: Skapa ett CI/CD-arbetsflöde för Azure våren Cloud med GitHub-åtgärder
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538472"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure våren Cloud CI/CD med GitHub-åtgärder

GitHub-åtgärder har stöd för ett arbets flöde för automatisk program varu utvecklings livs cykel. Med GitHub-åtgärder för Azure våren Cloud kan du skapa arbets flöden i din lagrings plats för att skapa, testa, paketera, lansera och distribuera dem till Azure. 

## <a name="prerequisites"></a>Förutsättningar
Det här exemplet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Konfigurera GitHub-lagringsplatsen och autentisera
Du behöver en Azure Service-princip för att godkänna Azure login-åtgärden. Kör följande kommandon på den lokala datorn för att få en Azure-autentiseringsuppgift:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
För att få åtkomst till en speciell resurs grupp kan du minska omfånget:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Kommandot ska generera ett JSON-objekt:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

I det här exemplet används [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) -exemplet på GitHub.  Förgrena exemplet, öppna GitHub-lagringsplatsen och klicka på fliken **Inställningar** . Öppna **hemligheter** -menyn och klicka på **Lägg till en ny hemlighet**:

 ![Lägg till ny hemlighet](./media/github-actions/actions1.png)

Ange det hemliga namnet `AZURE_CREDENTIALS` och dess värde till JSON-strängen som du hittade under rubriken *Konfigurera din GitHub-lagringsplats och autentisera*.

 ![Ange hemliga data](./media/github-actions/actions2.png)

Du kan också hämta autentiseringsuppgifter för Azure-inloggning från Key Vault i GitHub-åtgärder som beskrivs i [autentisera Azure-våren med Key Vault i GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Etablera tjänst instans
För att etablera din Azure våren Cloud Service-instans kör du följande kommandon med hjälp av Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Bygg arbets flödet
Arbets flödet definieras med hjälp av följande alternativ.

### <a name="prepare-for-deployment-with-azure-cli"></a>Förbereda för distribution med Azure CLI
Kommandot `az spring-cloud app create` är för närvarande inte idempotenta.  Vi rekommenderar det här arbets flödet på befintliga Azure våren-molnappar och-instanser.

Använd följande Azure CLI-kommandon för att förbereda:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuera med Azure CLI direkt
Skapa `.github/workflow/main.yml`-filen på lagrings platsen:

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
Den senaste versionen av Azure CLI används av AZ `run` kommandot. Om det finns andra ändringar kan du även använda en speciell version av Azure CLI med Azure/CLI `action`. 

> [!Note] 
> Det här kommandot körs i en ny behållare, så `env` fungerar inte, och fil åtkomst för kors åtgärder kan ha extra begränsningar.

Skapa filen. GitHub/Workflow/main. yml på lagrings platsen:
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

## <a name="deploy-with-maven-plugin"></a>Distribuera med maven-plugin-programmet
Ett annat alternativ är att använda [maven-plugin-programmet](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) för att distribuera jar-och uppdaterings inställningarna för appen. Kommandot `mvn azure-spring-cloud:deploy` är idempotenta och skapar automatiskt appar om så behövs. Du behöver inte skapa motsvarande appar i förväg.

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

## <a name="run-the-workflow"></a>Köra arbetsflödet
GitHub- **åtgärder** ska aktive ras automatiskt när du har push-överför `.github/workflow/main.yml` till GitHub. Åtgärden utlöses när du push-överför en ny incheckning. Om du skapar filen i webbläsaren bör åtgärden redan ha körts.

Verifiera att åtgärden har Aktiver ATS genom att klicka på fliken **åtgärder** på sidan GitHub-lagringsplats:

 ![Verifiera aktive rad åtgärd](./media/github-actions/actions3.png)

Om åtgärden körs som ett fel, till exempel om du inte har angett Azure-autentiseringsuppgiften, kan du köra kontrollerna igen efter att ha åtgärdat felet. På sidan GitHub-lagringsplats klickar du på **åtgärder**, väljer den angivna arbets flödes uppgiften och klickar sedan på knappen **Kör om kontroller** för att köra om kontrollerna:

 ![Kör om kontroller](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Nästa steg
* [Key Vault för GitHub-åtgärder för våren Cloud](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory tjänstens huvud namn](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub-åtgärder för Azure](https://github.com/Azure/actions/)
