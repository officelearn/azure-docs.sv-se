---
title: Använd GitHub-åtgärder för att göra kod uppdateringar i Azure Functions
description: Lär dig hur du använder GitHub-åtgärder för att definiera ett arbets flöde för att skapa och Distribuera Azure Functions-projekt i GitHub.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 483ac9380fa8d58f294112cb6c80e0393fa01589
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028960"
---
# <a name="continuous-delivery-by-using-github-action"></a>Kontinuerlig leverans med hjälp av GitHub-åtgärd

Med [GitHub-åtgärder](https://github.com/features/actions) kan du definiera ett arbets flöde för att automatiskt skapa och distribuera din funktions kod till Function-appen i Azure. 

> [!IMPORTANT]  
> GitHub-åtgärder är för närvarande Beta versioner. Du måste först [Registrera dig för att kunna ansluta till förhands granskningen](https://github.com/features/actions) med ditt GitHub-konto.

I GitHub-åtgärder är ett [arbets flöde](https://help.github.com/articles/about-github-actions#workflow) en automatiserad process som du definierar i din GitHub-lagringsplats. Den här processen visar GitHub hur du skapar och distribuerar dina Functions-AppData på GitHub. 

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/`-sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet. 

För ett Azure Functions-arbetsflöde har filen tre delar: 

| Section | Uppgifter |
| ------- | ----- |
| **Autentisering** | <ol><li>Definiera ett huvud namn för tjänsten.</li><li>Ladda ned publicerings profil.</li><li>Skapa en GitHub-hemlighet.</li></ol>|
| **Konstruktion** | <ol><li>Konfigurera miljön.</li><li>Bygg in Function-appen.</li></ol> |
| **Distribuera** | <ol><li>Distribuera Function-appen.</li></ol>|

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa ett [huvud namn för tjänsten](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med hjälp av kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i [Azure CLI](/cli/azure/). Du kan köra det här kommandot med [Azure Cloud Shell](https://shell.azure.com) i Azure Portal eller genom att välja knappen **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

I det här exemplet ersätter du plats hållarna i resursen med ditt prenumerations-ID, resurs grupp och funktionens program namn. Utdata är de autentiseringsuppgifter för roll tilldelning som ger åtkomst till din Function-app. Kopiera det här JSON-objektet, som du kan använda för att autentisera från GitHub.

> [!NOTE]
> Du behöver inte skapa ett huvud namn för tjänsten om du bestämmer dig för att använda publicerings profilen för autentisering.

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minimal åtkomst. Detta är anledningen till att omfånget i föregående exempel är begränsat till den särskilda Function-appen och inte hela resurs gruppen.

## <a name="download-the-publishing-profile"></a>Ladda ned publicerings profilen

Du kan ladda ned publicerings profilen för din functionapp genom att gå till sidan **Översikt** i appen och klicka på **Hämta publicerings profil**.

   ![Ladda ned publicerings profil](media/functions-how-to-github-actions/get-publish-profile.png)

Kopiera innehållet i filen.

## <a name="configure-the-github-secret"></a>Konfigurera GitHub-hemligheten

1. I [GitHub](https://github.com), bläddra i din lagrings plats, välj **Inställningar** > **hemligheter** > **Lägg till en ny hemlighet**.

   ![Lägg till hemlighet](media/functions-how-to-github-actions/add-secret.png)

1. Använd `AZURE_CREDENTIALS` för **namnet** och det kopierade kommandot utdata för **värde**, om du sedan väljer **Lägg till hemlighet**. Om du använder publicerings profil använder du `SCM_CREDENTIALS` som **värde**för **namn** och fil innehåll.

GitHub kan nu autentisera till din Function-app i Azure.

## <a name="set-up-the-environment"></a>Konfigurera miljön 

Konfigurationen av miljön kan göras med hjälp av en av publicerings konfigurations åtgärderna.

|Språk | Installations åtgärd |
|---------|---------|
|**NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

I följande exempel visas en del av arbets flödet som konfigurerar miljön för de olika språk som stöds:

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>Bygg in Function-appen

Detta beror på språket och för språk som stöds av Azure Functions, bör det här avsnittet vara standard stegen för version av varje språk.

I följande exempel visas den del av arbets flödet som bygger på-funktions programmet i de olika språk som stöds.:

**JavaScript**

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

**Python**

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

**NET**

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

**Java**

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

## <a name="deploy-the-function-app"></a>Distribuera funktionsappen

Om du vill distribuera din kod till en Function-app måste du använda åtgärden `Azure/functions-action`. Den här åtgärden har två parametrar:

|Parameter |Förklaring  |
|---------|---------|
|**_App-Name_** | Erforderlig Namnet på din Function-app. |
|_**plats namn**_ | Valfritt Namnet på den [distributions plats](functions-deployment-slots.md) som du vill distribuera till. Facket måste redan vara definierat i din Function-app. |


I följande exempel används version 1 av `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Nästa steg

Om du vill visa ett fullständigt arbets flöde. yaml, se en av filerna i [arbets flödes exemplen för Azure GitHub-åtgärder lagrings platsen](https://github.com/Azure/actions-workflow-samples) som har `functionapp` i namnet. Du kan använda dessa exempel som start punkt för arbets flödet.

> [!div class="nextstepaction"]
> [Läs mer om GitHub-åtgärder](https://help.github.com/en/articles/about-github-actions)
