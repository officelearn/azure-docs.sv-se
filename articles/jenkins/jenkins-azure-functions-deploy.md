---
title: Distribuera till Azure Functions med hjälp av plugin-programmet Jenkins i Azure Functions
description: Lär dig att distribuera till Azure Functions med hjälp av plugin-programmet Jenkins i Azure Functions
ms.service: jenkins
keywords: jenkins, azure, devops, java, azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: bd8fa10ca0a9809891efc67ff930ab01d502eda9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640970"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Distribuera till Azure Functions med hjälp av plugin-programmet Jenkins i Azure Functions

[Azure Functions](/azure/azure-functions/) är en "serverlös" beräkningstjänst. Med Azure Functions kan köra du kod på begäran utan att tillhandahålla eller hantera infrastruktur. I den är självstudien får du lära dig att distribuera en Java-funktion till Azure Functions med hjälp av Azure Functions-plugin-programmet.

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- **Jenkins-server**: Om du inte har en Jenkins-server installerad kan du läsa artikeln [Skapa en Jenkins-server i Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > Källkoden i den här självstudien finns i [Visual Studio Kina GitHub-lagringsplatsen](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Skapa en Java-funktion

Om du vill skapa en Java-funktion med Java-körningsstacken kan du använda antingen [Azure-portalen](https://portal.azure.com) eller [Azure CLI](/cli/azure/?view=azure-cli-latest).

Följande steg visar hur du skapar en Java-funktion med Azure CLI:

1. Skapa en resursgrupp som ersätter platshållaren **&lt;resource_group >** med resursgruppens namn.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Skapa ett Azure Storage-konto som ersätter platshållarna med lämpliga värden.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Skapa testfunktionsappen som ersätter platshållarna med lämpliga värden.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. Uppdatera till version 2.x-körningen som ersätter platshållarna med lämpliga värden.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Förbereda Jenkins-servern

Följande steg beskriver hur du förbereder Jenkins-servern:

1. Distribuera en [Jenkins-server](https://aka.ms/jenkins-on-azure) i Azure. Om du inte redan har en instans av Jenkins-servern installerad får du vägledning genom processen i artikeln [Skapa en Jenkins-server i Azure](./install-jenkins-solution-template.md).

1. Logga in på Jenkins-instansen med SSH.

1. Installera maven med följande kommando på Jenkins-instansen:

    ```terminal
    sudo apt install -y maven
    ```

1. På Jenkins-instansen installerar du [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) genom att utfärda följande kommandon vid en terminaluppmaning:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Installera följande plugin-program i Jenkins-instrumentpanelen:

    - Azure Functions Plugin
    - EnvInject-plugin-programmet

1. Jenkins behöver Azure-tjänstens huvudnamn för att autentisera och få åtkomst till Azure-resurser. Referera till de stegvisa instruktionerna i [Distribuera till Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md).

1. Med Azure-tjänstens huvudnamn lägger du till ”Microsoft Azure Service Principal” som autentiseringstyp i Jenkins. Referera till självstudien [Distribuera till Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="fork-the-sample-github-repo"></a>Förgrening GitHub-exempellager

1. [Logga in på GitHub-lagringsplatsen för appen udda eller även exempel](https://github.com/VSChina/odd-or-even-function.git).

1. I det övre högra hörnet i GitHub väljer du **Fork** (Förgrening).

1. Följ anvisningarna för att välja ditt GitHub-konto och slutför förgreningen.

## <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins-pipeline

I det här avsnittet skapar du en [Jenkins-pipeline](https://jenkins.io/doc/book/pipeline/).

1. Skapa en pipeline i Jenkins-instrumentpanelen.

1. Aktivera **Förbered en miljö för körningen**.

1. Lägg till följande miljövariabler i rutan för **egenskapsinnehåll**, som ersätter platshållarna med lämpliga värden för din miljö:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. I avsnittet **Pipeline->Definition** väljer du **Pipeline-skript från SCM**.

1. Ange din GitHub-förgrening URL och skript sökväg (”doc/resurser/jenkins/JenkinsFile”) ska användas i den [JenkinsFile exempel](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Skapa och distribuera

Nu är det dags att köra Jenkins-jobbet.

1. Först hämtar du auktoriseringsnyckeln via anvisningarna i artikeln om [HTTP-utlösare och bindningar i Azure Functions](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys).

1. Ange appens webbadress i webbläsaren. Ersätt platshållarna med lämpliga värden och ange ett numeriskt värde för **&lt;input_number >** som indata för funktionen Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Du ser resultat som liknar följande exempelutdata (där ett udda tal – 365 – användes som ett test):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda det här programmet tar du bort de resurser som du skapade med följande steg:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Functions i följande resurs:
> [!div class="nextstepaction"]
> [Azure Functions-dokumentation](/azure/azure-functions/)
