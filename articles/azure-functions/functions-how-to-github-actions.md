---
title: Använda GitHub-åtgärder för att skapa koduppdateringar i Azure Functions
description: Lär dig hur du använder GitHub-åtgärder för att definiera ett arbetsflöde för att skapa och distribuera Azure Functions-projekt i GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878212"
---
# <a name="continuous-delivery-by-using-github-action"></a>Kontinuerlig leverans med hjälp av GitHub Action

[Med GitHub-åtgärder](https://github.com/features/actions) kan du definiera ett arbetsflöde för att automatiskt skapa och distribuera din funktionskod för att fungera app i Azure. 

I GitHub-åtgärder är ett [arbetsflöde](https://help.github.com/articles/about-github-actions#workflow) en automatiserad process som du definierar i GitHub-databasen. Den här processen talar om för GitHub hur du skapar och distribuerar ditt funktionsappprojekt på GitHub. 

Ett arbetsflöde definieras av en YAML-fil (.yml) i `/.github/workflows/` sökvägen i databasen. Den här definitionen innehåller de olika steg och parametrar som utgör arbetsflödet. 

För ett Azure Functions-arbetsflöde har filen tre avsnitt: 

| Section | Aktiviteter |
| ------- | ----- |
| **Autentisering** | <ol><li>Definiera ett huvudnamn för tjänsten.</li><li>Ladda ner publiceringsprofilen.</li><li>Skapa en GitHub-hemlighet.</li></ol>|
| **Utveckla** | <ol><li>Ställ in miljön.</li><li>Skapa funktionsappen.</li></ol> |
| **Distribuera** | <ol><li>Distribuera funktionsappen.</li></ol>|

> [!NOTE]
> Du behöver inte skapa ett tjänsthuvudnamn om du bestämmer dig för att använda publiceringsprofilen för autentisering.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [tjänsthuvudnamn](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com) i Azure-portalen eller genom att välja knappen **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

I det här exemplet ersätter du platshållarna i resursen med ditt prenumerations-ID, resursgrupp och funktionsappnamn. Utdata är de autentiseringsuppgifter för rolltilldelning som ger åtkomst till din funktionsapp. Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

> [!IMPORTANT]
> Det är alltid en god praxis att ge minimal tillgång. Det är därför omfånget i föregående exempel är begränsat till den specifika funktionsappen och inte hela resursgruppen.

## <a name="download-the-publishing-profile"></a>Ladda ner publiceringsprofilen

Du kan ladda ned publiceringsprofilen för din funktionsapp genom att gå till sidan **Översikt i** appen och klicka på **Hämta publiceringsprofil**.

   ![Ladda ner publicera profil](media/functions-how-to-github-actions/get-publish-profile.png)

Kopiera innehållet i filen.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

1. I GitHub väljer du **Inställningar** > **hemligheter** > **Lägg till en ny hemlighet**i [GitHub.](https://github.com)

   ![Lägg till hemlighet](media/functions-how-to-github-actions/add-secret.png)

1. Lägg till en ny hemlighet.

   * Om du använder tjänstens huvudnamn som du skapade `AZURE_CREDENTIALS` med hjälp av Azure CLI använder du för **namnet**. Klistra sedan in det kopierade JSON-objektutdata för **Värde**och välj **Lägg till hemligt**.
   * Om du använder en publiceringsprofil använder du `SCM_CREDENTIALS` namnet . **Name** Använd sedan publiceringsprofilens filinnehåll för **Värde**och välj **Lägg till hemligt**.

GitHub kan nu autentisera till din funktionsapp i Azure.

## <a name="set-up-the-environment"></a>Konfigurera miljön 

Inställningen av miljön görs med hjälp av en språkspecifik publiceringsinställningsåtgärd.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas den del `actions/setup-node` av arbetsflödet som använder åtgärden för att ställa in miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas den del `actions/setup-python` av arbetsflödet som använder åtgärden för att ställa in miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas den del `actions/setup-dotnet` av arbetsflödet som använder åtgärden för att ställa in miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas den del `actions/setup-java` av arbetsflödet som använder åtgärden för att ställa in miljön:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Skapa funktionsappen

Detta beror på språket och för språk som stöds av Azure Functions, bör det här avsnittet vara standardbyggstegen för varje språk.

I följande exempel visas den del av arbetsflödet som bygger funktionsappen, som är språkspecifik:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Distribuera funktionsappen

Om du vill distribuera koden till en funktionsapp måste du använda `Azure/functions-action` åtgärden. Den här åtgärden har två parametrar:

|Parameter |Förklaring  |
|---------|---------|
|**_app-namn_** | (Obligatoriskt) Namnet på funktionsappen. |
|_**kortplatsnamn**_ | (Valfritt) Namnet på [distributionsplatsen](functions-deployment-slots.md) som du vill distribuera till. Kortplatsen måste redan ha definierats i funktionsappen. |


I följande exempel används version `functions-action`1 av :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Nästa steg

Om du vill visa ett fullständigt arbetsflöde .yaml läser du en av filerna `functionapp` i [arbetsflödesexemplen för Azure GitHub-åtgärder](https://aka.ms/functions-actions-samples) som har i namnet. Du kan använda dessa exempel som en startpunkt för arbetsflödet.

> [!div class="nextstepaction"]
> [Läs mer om GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions)
