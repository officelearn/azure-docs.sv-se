---
title: Distribuera Resource Manager-mallar med GitHub-åtgärder
description: Beskriver hur du distribuerar Azure Resource Manager-mallar med hjälp av GitHub-åtgärder.
ms.topic: conceptual
ms.date: 07/02/2020
ms.custom: github-actions-azure
ms.openlocfilehash: cea099088005fa91e1b3e9a793105df4796a66ee
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018584"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Distribuera Azure Resource Manager-mallar med GitHub-åtgärder

Med [GitHub-åtgärder](https://help.github.com/en/actions) kan du skapa anpassade arbets flöden för program varu utveckling, direkt i din GitHub-lagringsplats där Azure Resource Manager (arm)-mallarna lagras. Ett [arbets flöde](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) definieras av en yaml-fil. Arbets flöden har ett eller flera jobb med varje jobb som innehåller en uppsättning steg som utför enskilda uppgifter. Steg kan köra kommandon eller använda en åtgärd. Du kan skapa egna åtgärder eller använda åtgärder som delas av [GitHub-communityn](https://github.com/marketplace?type=actions) och anpassa dem efter behov. Den här artikeln visar hur du använder [Azure CLI-åtgärder](https://github.com/marketplace/actions/azure-cli-action) för att distribuera Resource Manager-mallar.

Azure CLI-åtgärden har två beroende åtgärder:

- **[Checka ut: ta](https://github.com/marketplace/actions/checkout)** en titt på lagrings platsen så att arbets flödet kan komma åt en viss Resource Manager-mall.
- **[Azure-inloggning](https://github.com/marketplace/actions/azure-login)**: Logga in med dina Azure-autentiseringsuppgifter

Ett grundläggande arbets flöde för att distribuera en Resource Manager-mall kan ha tre steg:

1. Kolla ut en mallfil.
2. Logga in i Azure.
3. Distribuera en Resource Manager-mall

## <a name="prerequisites"></a>Förutsättningar

Du behöver en GitHub-lagringsplats för att lagra dina Resource Manager-mallar och dina arbetsflödes-filer. Information om hur du skapar [en ny databas finns i skapa en ny lagrings plats](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>Konfigurera autentiseringsuppgifter för distribution

Azure login-åtgärden använder ett huvud namn för tjänsten för att autentisera mot Azure. Huvud kontot för ett CI/CD-arbetsflöde behöver vanligt vis den inbyggda deltagar rättigheten för att kunna distribuera Azure-resurser.

Följande Azure CLI-skript visar hur du skapar ett tjänst huvud namn för Azure med deltagar behörighet för en Azure-resurs grupp. Den här resurs gruppen är den plats där arbets flödet distribuerar de resurser som definierats i din Resource Manager-mall.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Anpassa värdet för **$projectName** och **$location** i skriptet. Resurs gruppens namn är projekt namnet med **RG** tillagt. Du måste ange resurs gruppens namn i ditt arbets flöde.

Skriptet skriver ut ett JSON-objekt liknande detta:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Kopiera JSON-utdata och lagra den som en GitHub-hemlighet i din GitHub-lagringsplats. Se [förutsättningen](#prerequisites) om du inte har en lagrings plats ännu.

1. Från din GitHub-lagringsplats väljer du fliken **Inställningar** .
1. Välj **hemligheter** på den vänstra menyn.
1. Ange följande värden:

    - **Namn**: AZURE_CREDENTIALS
    - **Värde**: (klistra in JSON-utdata)
1. Välj **Lägg till hemlighet**.

Du måste ange det hemliga namnet i arbets flödet.

## <a name="add-resource-manager-template"></a>Lägg till Resource Manager-mall

Lägg till en Resource Manager-mall i GitHub-lagringsplatsen. Om du inte har någon kan du använda följande mall. Mallen skapar ett lagrings konto.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Du kan lagra filen var som helst på lagrings platsen. Arbets flödes exemplet i nästa avsnitt förutsätter att mallfilen heter **azuredeploy.jspå**, och den lagras i en mapp med namnet **mallar** i lagrings platsens rot.

## <a name="create-workflow"></a>Skapa arbets flöde

Arbets flödes filen måste lagras i mappen **. GitHub/arbets flöden** i roten på din lagrings plats. Arbets flödets fil namns tillägg kan vara antingen **. yml** eller **. yaml**.

Du kan antingen skapa en arbets flödes fil och sedan skicka/överföra filen till lagrings platsen eller använda följande procedur:

1. Från din GitHub-lagringsplats väljer du **åtgärder** på den översta menyn.
1. Välj **nytt arbets flöde**.
1. Välj **Konfigurera ett arbets flöde själv**.
1. Byt namn på arbets flödes filen om du vill ha ett annat namn än **main. yml**. Till exempel: **deployStorageAccount. yml**.
1. Ersätt innehållet i YML-filen med följande:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Arbets flödes filen har tre delar:

    - **namn**: namnet på arbets flödet.
    - **på**: namnet på de GitHub-händelser som utlöser arbets flödet. Arbets flödet utlöses när det finns en push-händelse på huvud grenen, vilket ändrar minst en av de angivna två filerna. De två filerna är arbets flödes filen och mallfilen.

        > [!IMPORTANT]
        > Kontrol lera att de två filerna och deras sökvägar stämmer överens.
    - **jobb**: en arbets flödes körning består av ett eller flera jobb. Det finns bara ett jobb med namnet **Deploy-Storage-Account-Template**.  Det här jobbet har tre steg:

        - **Käll kod för utcheckning**.
        - **Logga in på Azure**.

            > [!IMPORTANT]
            > Kontrol lera att det hemliga namnet matchar det du sparade i lagrings platsen. Se [Konfigurera autentiseringsuppgifter för distribution](#configure-deployment-credentials).
        - **Distribuera arm-mall**. Ersätt värdet för **resourceGroupName**.  Om du använde Azure CLI-skriptet i [Konfigurera autentiseringsuppgifter för distribution](#configure-deployment-credentials), är det genererade resurs grupps namnet projekt namnet med **RG** tillagt. Verifiera värdet för **templateLocation**.

1. Välj **Starta genomförande**.
1. Välj **genomför direkt på huvud grenen**.
1. Välj **genomför ny fil** (eller **genomför ändringar**).

Eftersom arbets flödet har kon figurer ATS för att utlösas av antingen arbets flödes filen eller mallfilen som uppdateras, startar arbets flödet direkt efter att du har bekräftat ändringarna.

## <a name="check-workflow-status"></a>Kontrol lera arbets flödes status

1. Välj fliken **åtgärder** . Du får se ett **skapa deployStorageAccount. yml** -arbetsflöde i listan. Det tar 1-2 minuter att köra arbets flödet.
1. Välj arbets flödet för att öppna det.
1. Välj **distribuera-Storage-Account-Template** (jobbnamn) på den vänstra menyn. Jobb namnet har definierats i arbets flödet.
1. Välj **distribuera arm-mall** (steg namn) för att expandera den. Du kan se REST API svaret.

## <a name="next-steps"></a>Nästa steg

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i [Självstudier: skapa och distribuera din första arm-mall](template-tutorial-create-first-template.md).
